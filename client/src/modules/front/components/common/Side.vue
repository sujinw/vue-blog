<template>
  <div class="sideBox">
    <div class="sideBox__mask" @click="closeSideBox"></div>
    <div class="sideBox__main" :class="{ 'sideBox__main--open': sideBoxOpen}">
      <img src="http://7xp9v5.com1.z0.glb.clouddn.com/touxiang.png" alt="" class="sideBox__img" @click="backToIndex">
      <p class="sideBox__name">小深刻的秋鼠</p>
      <p class="sideBox__motto">Love Life, Love sharing</p>
      <ul class="sideBox__iconList">
        <li v-for="icon in iconList" class="sideBox__iconItem">
          <a :href="icon.href"><i class="iconfont" :class="'icon-'+icon.name"></i></a>
        </li>
      </ul>
      <ul class="sideBox__tagList" v-if="isInList">
        <li v-for="tag in tagList" class="sideBox__tagItem" :class="{ 'sideBox__tagItem--active': (typeof selectTagArr.find(function(e){return e.id == tag.id}) !== 'undefined')}" @click="toggleSelect(tag.id, tag.name)">
          <span>{{tag.name}}</span>
        </li>
      </ul>
      <div class="categoryBox" v-if="!isInList" :class="{ 'categoryBox--fixed': (scrollTop > 270)}" ref="categoryBox">
        <p class="categoryBox__title">文章目录</p>
        <ul class="categoryBox__list">
          <li v-for="item in category" :class="'categoryBox__'+item.tagName">
            <a :href="item.href">{{item.text}}</a>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import tagApi from 'api/tag.js'
import throttle from 'lib/throttle.js'
export default {
  name: 'sideBox',
  data() {
    return {
      tagList: [],
      selectTagArr: [],
      sideBoxOpen: false,
      scrollTop: 0,
      iconList: [{
        name: 'github',
        href: 'https://github.com/BUPT-HJM'
      }]
    }
  },
  props: {
    isInList: {
      type: Boolean,
      required: true
    },
    category: {
      type: Array,
      required: false
    }
  },
  mounted() {
    tagApi.getAllTags().then(res => {
      this.tagList = res.data.tagArr;
    })
  },
  created() {
    console.log('side created')
    this.$eventBus.$on('toggleSideBox', this.toggleSideBox);
    this.$eventBus.$on('closeSideBox', this.closeSideBox);
    this.$eventBus.$on('clearSelectTagArr', this.clearSelectTagArr)
    if(!this.isInList) {
       window.onscroll = throttle(this.getScrollTop, 30)
    } 
  },
  beforeDestroy() {
    console.log('side beforeDestroy')
    window.onscroll = null
    this.$eventBus.$off('toggleSideBox', this.toggleSideBox);
    this.$eventBus.$off('closeSideBox', this.closeSideBox);
    this.$eventBus.$off('clearSelectTagArr', this.clearSelectTagArr)
  },
  methods: {
    backToIndex() {
      this.$router.push('/')
    },
    toggleSideBox() {
      this.sideBoxOpen = !this.sideBoxOpen
    },
    closeSideBox() {
      this.sideBoxOpen = false
    },
    toggleSelect(id, name) {
      if (typeof this.selectTagArr.find(function(e) {
          return e.id == id
        }) == 'undefined') {
        this.selectTagArr.push({
          id,
          name
        })
      } else {
        this.selectTagArr = this.selectTagArr.filter((e) => {
          return e.id !== id;
        })
      }
      this.$eventBus.$emit('filterListByTag', {
        tag: this.selectTagArr
      });
    },
    getScrollTop() {
      let scrollTop = 0,
        bodyScrollTop = 0,
        documentScrollTop = 0;　　
      if (document.body) {　　　　
        bodyScrollTop = document.body.scrollTop;　　
      }　　
      if (document.documentElement) {　　　　
        documentScrollTop = document.documentElement.scrollTop;　　
      }　　
      this.scrollTop = (bodyScrollTop - documentScrollTop > 0) ? bodyScrollTop : documentScrollTop;
      console.log(this.scrollTop)
    },
    clearSelectTagArr() {
      this.selectTagArr = []
    }
  },
  computed: {},
  watch: {
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" scoped>
  @import '../../assets/stylus/_settings.styl'
  .sideBox
    width 250px
    float left
    text-align center
    &__img
      width 150px
      border-radius 50%
      box-shadow 0 0 2px black
      margin-top 10px
      cursor pointer
    &__name
      color $grey-dark
      font-size 20px
      margin-top 5px
      margin-bottom 5px
    &__motto
      color $grey
      margin-bottom 8px
    &__iconList
      list-style none
      margin-bottom 8px
    &__iconItem
      display inline-block
      cursor pointer
      a
        text-decoration none
        color $grey
        .iconfont
          font-size 28px
          &:hover
            color black
      // &:hover
      //   color $blue
    &__tagList
      list-style none
      padding 10px
    &__tagItem
      display inline-block
      border 1px solid $grey
      border-radius 4px
      margin 5px
      padding 10px
      color $grey
      cursor pointer
      &:hover
        color $blue
    &__tagItem--active
      color $blue
      border 1px solid $blue
    .categoryBox
      padding-left 20px
      padding-right 15px
      &__title
        margin-top 15px
        margin-bottom 10px
        font-weight 400
        color #808080
        font-size 18px
      ul
        list-style none
      li
        text-align left
        //list-style-type: square;
        margin-bottom 5px
        padding-left 20px
        word-wrap break-word
        word-break all
        a
          color $grey
          text-decoration none
          margin-left -18px
          word-wrap break-word
          word-break break-all
          &:hover
            color $blue 
            text-decoration underline
      &__h1 
        margin-left 0 
      &__h2
        margin-left 20px
      &__h3
        margin-left 40px
      &__h4
        margin-left 60px
      &__h5
        margin-left 80px 
      &__h6
        margin-left 100px
    &__mask
      display none
    .categoryBox--fixed
      position fixed
      top 60px 
      bottom 0
      overflow-y auto
      width 250px     
  @media screen and (max-width: 850px) 
    .sideBox
      position absolute
      top 0 
      left 0
      &__main
        position fixed
        left 0px
        top 60px
        bottom 0
        width 250px
        transform translateX(-250px)
        -webkit-transform translateX(-250px)
        transition transform 0.3s
        -webkit-transtion transform 0.3s
        background-color white
        overflow-x hidden
        overflow-y auto
        &--open
          box-shadow: 0 0 10px rgba(0,0,0,0.2);
          z-index 2
          transform translateX(0px)
          -webkit-transform translateX(0px)
          transition transform 0.3s
          -webkit-transtion transform 0.3s
      &__mask
        position fixed
        top 60px
        left 250px
        right 0
        bottom 0
        display block
        z-index 1
      &__tagItem:hover
        color $grey
      &__tagItem--active:hover
        color $blue
      .categoryBox--fixed
        position static
        width auto
</style>
<style>
  @import '../../assets/iconfont/iconfont.css'
</style>

