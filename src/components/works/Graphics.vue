<template lang="pug">
.graphics-container
  .graphics-tags
    .graphics-tags-item(v-for="(tag, index) in tagList" @click="enableTag(index)" :class="[activeTagFlags[index] ? activeClass : '', inactiveClass]") {{displayTagList[index]}}
  transition-group.graphics-item-container(name="filter")
    .item(v-for="(item, index) in sortedData"
     :style="xmove"
     :key="item.filename"
     @click="showModal(index)")
      img.item-img(:style="{objectPosition: item.position}" v-lazy="'/twilight/img/thumbnail/'+item.filename+item.thm_ext")
  graphics-modal(v-if="isShow" :data="sortedData" :index="showIndex" @close="emitEvent")
</template>

<script>
import graphicsData from "@/assets/graphicsData.json";
import GraphicsModal from "@/components/works/GraphicsModal.vue";

export default {
  name: "Graphics",
  data() {
    return {
      graphicsData,
      tagList: ["プロダクト", "イラスト", "ドット絵", "デザイン"],
      displayTagList: ["Product", "Illust", "Pixelart", "Design"],
      activeTagFlags: [false, false, false, false],
      activeTagIndex: -1,
      oldActiveTagIndex: 0,
      activeClass: "graphics-tags-item-active",
      inactiveClass: "graphics-tags-item",
      isShow: false,
      showIndex: 0,
      show: true
    };
  },
  computed: {
    filteredData() {
      let data = this.graphicsData.slice();
      let t = this.tagList;
      let ati = this.activeTagIndex;
      if (this.activeTagIndex == -1) {
      } else {
        data = data.filter(function(item, index) {
          let isIncludeTag = item.tags.some(a => a == t[ati]);
          return isIncludeTag;
        });
      }
      return data;
    },
    sortedData() {
      let data = this.filteredData.slice();
      data = data.sort(function(a, b) {
        return b.date - a.date;
      });
      return data;
    },
    xmove() {
      return {
        "--xmove":
          this.activeTagIndex == -1
            ? 0
            : this.oldActiveTagIndex - this.activeTagIndex
      };
    },
    height() {
      return {
        "--height": window.innerHeight + "px"
      };
    }
  },
  methods: {
    emitEvent(show) {
      this.isShow = show;
    },
    showModal(index) {
      this.isShow = true;
      this.showIndex = index;
    },
    enableTag(index) {
      this.oldActiveTagIndex = this.activeTagIndex;
      if (this.activeTagIndex == index) {
        this.activeTagIndex = -1;
        this.activeTagFlags.fill(false);
      } else {
        this.activeTagIndex = index;
        this.activeTagFlags.fill(false);
        this.activeTagFlags[index] = true;
      }
    },
    isEqual(a, b) {
      return a == b;
    }
  },
  components: {
    GraphicsModal
  }
};
</script>

<style scoped lang="sass">

@import "@/assets/colors.sass"
@import "@/assets/media.sass"

.morph
  background-color: $color-bg
  color: $color-text
  box-shadow: $color-shadow
  border-radius: 10px

.graphics-container
  width: 100%
  height: 100%
  display: flex
  flex-direction: column

  .graphics-tags
    @extend .morph
    box-shadow: none
    width: 100%
    height: 8%
    display: flex
    justify-content: center
    align-items: center
    position: fixed

    .graphics-tags-item
      @extend .morph
      background-color: $color-base
      margin: 0 1vw 0 1vw
      height: 2rem
      padding: 0 2vw 0 2vw
      display: flex
      align-items: center
      justify-content: center
      cursor: pointer
      box-shadow: -5px -5px 15px $color-light, 5px 5px 15px $color-dark
      transition: all .1s
      user-select: none
      font-size: 15px
      font-family: vdl-logojr, sans-serif
      font-weight: 400
      font-style: normal

      @media only screen and (max-width: $md)
        font-size: 10px

    .graphics-tags-item-active
      @extend .graphics-tags-item
      box-shadow: inset -2px -2px 2px $color-light, inset 2px 2px 2px $color-dark

  .graphics-item-container
    width: 100%
    height: 80%
    position: absolute
    left: 0
    bottom: 0
    display: inline-flex
    justify-content: space-around
    flex-wrap: wrap
    overflow: scroll
    padding-top: 1vh
    padding-bottom: 3vh

    .item
      @extend .morph
      background-color: $color-base
      border-radius: 30px
      width: 41vh
      height: 33vh
      margin: 1vh
      border: solid 10px $color-base
      box-sizing: border-box
      box-shadow: 12px 12px 15px $color-dark

      &:hover
        box-shadow: 2px 2px 2px $color-dark, -2px -2px 2px $color-light
        transition: box-shadow .25s

      .item-img
        width: 100%
        height: 100%
        object-fit: cover
        border-radius: 30px

  .filter-enter-active, .filter-move
    transition: transform 1s ease .2s, opacity .3s ease .2s
    pointer-events: none

  .filter-leave-active
    position: absolute
    transition: transform 1s ease 0s, opacity .15s ease 0s
    pointer-events: none

  .filter-enter
    --xmove: 0

    opacity: 0
    transform: translateX(calc(-200px * var(--xmove)))

  .filter-leave-to
    opacity: 0
    transform: translateY(50vh)
</style>
