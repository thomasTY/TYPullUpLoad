<template>
  <div class="pull-up-load">
    <!-- <transition name="fade"> -->
      <div v-show="showLoading" class="load-more-box">
        <img v-show="showLoadingImg" id="load-more-img" :src="loadIcon">
        <div class="load-more-text">
          {{loadMoreText}}
        </div>
      </div>
    <!-- </transition> -->
  </div>
</template>

<script>
export default {
  name: 'PullUpLoad',
  props: {
    dataLoad: null,
    state: { // 外部赋值要采用 [1, new Date()]的方式，保证state每次的值都有变化，否则监听不到
      default: [] // 0.加载失败 1.加载完成 2.无更多数据
    }
  },
  data () {
    return {
      showLoading: false,
      showLoadingImg: true,
      loadingTimer: null, // 保存计时器
      navbarHeight: 0, // 导航条的高度
      loadMoreText: '加载更多',
      loadState: 0, // 0.收起状态 1.上拉状态 2.上拉超出可加载状态 3.正在加载状态 4.加载失败 5.加载完成 6.无更多数据
      loadStateTime: 600, // 展示状态的时间
      loadIcon: null,
      loadingImg: require('./loadingSpin1.svg'),
      arrowUpImg: require('./icon_arrow_up.png'),
      arrowDownImg: require('./icon_arrow_down.png'),
      successImg: require('./icon_success.png'),
      failImg: require('./iocn_erorr.png'),
      noMoreImg: require('./icon_no_more.png')
    }
  },
  watch: {
    // length: function
    state: function (newVal, oldVal) {
      if (newVal[0] === 0 || newVal[0] === 1 || newVal[0] === 2) {
        this.loadState = newVal[0] + 4
      } else {
        this.loadState = 5 // 加载完成
      }
    },
    loadState: function (val) {
      switch (val) {
        case 0: // 0.收起状态
          this.loadMoreText = '加载更多'
          this.loadIcon = null
          this.showLoading = false
          break
        case 1: // 1.上拉状态
          this.loadMoreText = '加载更多'
          this.loadIcon = this.arrowUpImg
          this.showLoading = true
          break
        case 2: // 2.上拉超出可加载状态
          this.loadMoreText = '加载更多'
          this.loadIcon = this.arrowDownImg
          this.showLoading = true
          break
        case 3: // 3.正在加载状态
          this.loadMoreText = '正在加载'
          this.loadIcon = this.loadingImg
          this.showLoading = true
          break
        case 4: // 4.加载失败
          this.loadMoreText = '加载失败'
          this.loadIcon = this.failImg
          this.restore()
          break
        case 5: // 5.加载完成
          this.loadMoreText = '加载完成'
          this.loadIcon = this.successImg
          this.restore()
          break
        case 6: // 6.无更多数据
          this.loadMoreText = '无更多数据'
          this.loadIcon = this.noMoreImg
          this.restore()
          break
      }
    }
  },
  created: function () {
    var agent = navigator.userAgent.toLowerCase()
    if (agent.match(/(iphone|ipod|ipad);?/i)) { // iOS
      this.navbarHeight = 64
    } else if (agent.match(/android/i)) { // Android
      this.navbarHeight = 72
    }
  },
  mounted: function () {
    this.listenScroll()
  },
  methods: {
    restore: function () {
      if (!this.loadingTimer) {
        var that = this
        that.loadingTimer = setTimeout(function () {
          that.loadState = 0
          clearTimeout(that.loadingTimer)
          that.loadingTimer = null
          window.onscroll = null // 清空onscroll，防止因为load-more-box的展示和隐藏造成滚动，从而造成死onscroll循环
          setTimeout(function () {
            that.listenScroll()
          }, 10)
        }, this.loadStateTime)
      }
    },
    toLoad: function () {
      if (this.loadState === 3) { // 正在加载的状态不用重复设置
        return
      }
      // loadState: 0.收起状态 1.上拉状态 2.上拉超出可加载状态 3.正在加载状态 4.加载失败 5.加载完成 6.无更多数据
      this.loadState = 3
      if (this.dataLoad) {
        var that = this
        this.loadingTimer = setTimeout(function () {
          clearTimeout(that.loadingTimer)
          that.loadingTimer = null
          that.dataLoad()
        }, 1000)
      }
    },
    listenScroll: function () {
      var that = this
      window.onscroll = function () { // 监听滚动的高度，实现上拉加载更多
        var screenHeight = window.screen.height // 屏幕高度
        var offset = (screenHeight + document.body.scrollTop - that.navbarHeight) - document.body.scrollHeight
        if (offset >= 0) {
          that.toLoad()
        }
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

/*------------------------上拉加载更多------------------------*/
.pull-up-load {
}
.load-more-box {
  padding: 15px 0px;
  display: -webkit-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: box;
  display: flex;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  box-pack: center;
  justify-content: center;
  -webkit-justify-content: center;
}
.fade-enter-active, .fade-leave-active {
  transition: height .25s;
  -webkit-transition: height .25s;
  -moz-transition: height .25s;
  -o-transition: height .25s;
}
.fade-enter, .fade-leave-active {
  height: 0px;
}
#load-more-img {
  width: 20px;
  height: 20px;
}
.load-more-text {
  text-align: center;
  font-size: 14px;
  color: #b2b2b2;
}
/*------------------------上拉加载更多------------------------*/
</style>
