<template lang='pug'>
.header(:style='{backgroundColor:config.title.color}') {{config.title.text}}

  .updated_info Updated on: {{updatedOn}}
  
  .edit_indicator(v-if="editMode" v-on:click="$store.commit('setEditMode', false)") Click to leave editmode
  a(href='https://github.com/Hoogkamer/vue-org-chart' target='_blank')
    img.gh(src="~/assets/img/gh.svg" title='Go to Github project page')
  search-box
  .menu
    file-menu(v-if='editMode')
    options-menu
    .home1
      a(href="/")
        i.material-icons.home(v-on:click="capture" title='Home') home
    .screenshot1(v-if='config.enableScreenCapture')
      i.material-icons.screenshot(v-on:click="capture" title='Save as image') photo_camera
  .info(v-if="config.information")
    i.material-icons.info_icon(v-on:click="infoOpen=!infoOpen") info
    .info-text(v-if="infoOpen")
      i.material-icons.info-close(v-on:click="infoOpen=false") close
      div(v-html="config.information")
</template>

<script>
import html2canvas from 'html2canvas'
import SearchBox from '~/components/SearchBox.vue'
import FileMenu from '~/components/FileMenu.vue'
import OptionsMenu from '~/components/OptionsMenu.vue'
import { mapState, mapActions } from 'vuex'
var panzoom = require('panzoom')
export default {
  components: { SearchBox, FileMenu, OptionsMenu },
  data: function() {
    return {
      infoOpen: false
    }
  },
  computed: {
    ...mapState(['chart', 'editMode', 'config', 'updatedOn'])
  },
  methods: {
    ...mapActions(['initZoom']),
    capture: function() {
      var area = document.querySelector('#chart')
      console.log(area)
      this.initZoom()
      setTimeout(x => {
        var x = document.querySelector('#chart')
        var svgContainer = document.querySelector('#chart')
        console.log(svgContainer)
        html2canvas(x, {
          onclone: element => {
            const svgElements = element.body.getElementsByTagName('svg')
            Array.from(svgElements).forEach(svgElement => {
              const bBox = svgElement.getBBox()
              svgElement.setAttribute('width', svgContainer.offsetWidth)
              svgElement.setAttribute('height', svgContainer.offsetHeight)
            })
          }
        }).then(canvas => {
          saveAs(canvas.toDataURL(), 'orgchart.png')
        })
      }, 500)
    }
  }
}
function saveAs(uri, filename) {
  var link = document.createElement('a')

  if (typeof link.download === 'string') {
    link.href = uri
    link.download = filename

    //Firefox requires the link to be in the body
    document.body.appendChild(link)

    //simulate click
    link.click()

    //remove the link when done
    document.body.removeChild(link)
  } else {
    window.open(uri)
  }
}
</script>

<style scoped>
.header {
  width: 100%;
  height: 50px;
  color: white;
  text-align: center;
  font-size: 40px;
  font-weight: 500;
  position: fixed;
  left: 0px;
  top: 0px;
  z-index: 2;
  box-shadow: 5px 2px 5px grey;
}
.screenshot1 {
  display: inline-block;
  box-sizing: content-box;
  position: absolute;
  top: 10px;
  left: 70px;
}
.screenshot,
.home {
  cursor: pointer;
}
.home1 a {
  color: white;
}
.home1 {
  display: inline-block;
  box-sizing: content-box;
  position: absolute;
  top: 10px;
  left: -50px;
}
.screenshot:hover,
.home:hover,
.info_icon:hover {
  border: 1px solid white;
}
.menu {
  position: absolute;
  top: 5px;
  left: 60px;
  font-size: 16px;
  width: 200px;
  text-align: left;
  padding: 10px;
}
.gh {
  position: absolute;
  top: 5px;
  right: 10px;
  width: 40px;
}
.info_icon {
  cursor: pointer;
}
.info {
  position: absolute;
  top: 0px;
  right: 60px;
}
.info-text {
  font-size: 14px;
  color: grey;
  width: 400px;
  padding: 20px;
}
.edit_indicator {
  position: absolute;
  top: 50px;
  right: 0px;
  font-size: 16px;
  background-color: red;
  padding: 5px;
  cursor: pointer;
}

.updated_info {
  position: absolute;
  top: 50px;
  right: 0px;
  font-size: 16px;
  color: grey;
  padding: 5px;
}
.info-text {
  width: 300px;
  background-color: white;
  border: 1px solid lightgrey;
  position: absolute;
  top: 40px;
  right: 20px;
  box-shadow: 2px 2px 2px grey;
}
.info-close {
  position: absolute;
  top: 5px;
  right: 5px;
  cursor: pointer;
  font-size: 14px;
}
</style>
