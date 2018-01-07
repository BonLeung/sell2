<template>
  <div class="food" v-show="showFlag">
    <div class="image-header">
      <img :src="food.image" alt="">
    </div>
    <split></split>
  </div>
</template>

<script>
import split from '@/components/split/split'

export default {
  props: {
    food: {
      type: Object
    }
  },
  data() {
    return {
      showFlag: false
    }
  },
  methods: {
    show() {
      this.showFlag = true
    },
    hide() {
      this.showFlag = false
    }
  },
  components: {
    split
  }
}
</script>

<style lang="stylus" scoped>
.food
  position: fixed;
  left: 0;
  top: 0;
  bottom: 48px;
  z-index: 30;
  width: 100%;
  background-color: #fff;
  .image-header
    position: relative;
    width: 100%;
    height: 0;
    padding-bottom: 100%;
    img
      position: absolute;
      left: 0;
      top: 0;
      width: 100%;
      height: 100%;
</style>


