<template lang="jade">
div.loc_pin(ref="pinDom", :style="pin_style")
  img.loc_icon(
    :width="loc.icon.width", 
    :height="loc.icon.height", 
    :style="icon_style", 
    :src="img_path",
    @click="show",
    @touchend="show"
    )
  div.loc_name(
    :style="name_style", 
    @click="show",
    @touchend="show"
    )
    | {{ loc.name }}
</template>
<script>
import {MapPos} from '../helpers/map_pos'
import pos_patch from '../helpers/pos_patch'
export default {
  props: {
    loc: {
      type: Object,
      required: true,
      map_pos: null,
    }
  },
  computed: {
    ...pos_patch,
    pin_pos(){
      return this.map_pos.sphe2rect(this.loc.pos)
    },
    pin_screen_pos(){
      let ww = window.innerWidth
      let wh = window.innerHeight
      let [px, py] = this.pin_pos
      let psx = Math.floor(ww/2 - (this.x - px) * this.zoom)
      let psy = Math.floor(wh/2 - (this.y - py) * this.zoom)
      return [psx, psy]
      // return [500, 500]
    },
    img_path(){
      return this.loc.icon.path
    },
    icon_style(){
      let [px, py] = this.pin_screen_pos
      let x = Math.floor(px + this.loc.icon.offset.x)
      let y = Math.floor(py + this.loc.icon.offset.y)
      return `left: ${x}px; top: ${y}px; ` + (this.loc.icon.icon_style || '')
    },
    name_style(){
      let [px, py] = this.pin_screen_pos
      let cw = this.loc.icon.style.name_width
      let x = Math.floor(px - cw / 2)
      let y = Math.floor(py)
      return `left: ${x}px; top: ${y}px; width: ${cw}px;` + (this.loc.icon.style.name_style || '')
    },
    pin_style(){
      let [px, py] = this.pin_screen_pos
      let x = Math.floor(px)
      let y = Math.floor(py)
      let w = Math.floor(this.loc.icon.style.name_width)
      return `left: ${x}px; top: ${y}px; width: ${w}px;`
    }
  },
  methods: {
    show(){
      this.$store.dispatch('show_location', this.loc)
    }
  },
  created(){
    this.map_pos = new MapPos(this.$store.state.map_info)
  }
}
</script>
<style>
.loc_pin{
  position: fixed;
  width: 60px;
}
.loc_name, .loc_icon{
  position: fixed;
  overflow: hidden;
  text-align: center;
}
</style>