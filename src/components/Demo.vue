<template>
  <div class="demo">
    <div class="title">
      TYPullUpLoad
    </div>
    <div class="tip">
      基于vue封装的上拉刷新组件，使用时为state每次赋值，要赋一个数组，数组第一个元素为实际state，第二个元素为new Date
      ，以标志变动，例如：[1, new Date()]
    </div>
    <div v-for="(item, index) in list" class="item">
      {{item}}
    </div>
    <pull-up-load :dataLoad="getMoreData" :state="pullLoadState">
    </pull-up-load>
  </div>
</template>

<script>
import PullUpLoad from './pullUpLoad/PullUpLoad.vue'

export default {
  name: 'Demo',
  data () {
    return {
      list: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], // 记录要展示的数据
      pullLoadState: [], // 下拉刷新和上拉刷新的状态 0.无数据 1.有数据 2.无更多数据
      timeout: null
    }
  },
  components: {
    'pull-up-load': PullUpLoad
  },
  methods: {
    // 加载更多数据，用于上拉刷新、首次加载
    getMoreData: function () {
      var that = this
      if (that.timeout) {
        return
      }
      that.timeout = setTimeout(function () {
        if (that.pullLoadState[0] === 0) {
          var last = that.list[that.list.length - 1]
          last += 1
          that.list.push(last)
          // PullUpLoad需要每次传入变化的值，所以每次赋值要取一个new Date
          that.pullLoadState = [1, new Date()]
        } else if (that.pullLoadState[0] === 1) {
          that.pullLoadState = [2, new Date()]
        } else {
          that.pullLoadState = [0, new Date()]
        }
        that.timeout = null
      }, 600)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.title {
  font-size: 20px;
  font-weight: bold;
  height: 30px;
  line-height: 30px;
  background-color: rgb(215, 215, 215);
}
.tip {
  background-color: rgb(215, 215, 215);
}
.item {
  width: 100%;
  height: 30px;
  line-height: 30px;
  text-align: center;
  border-bottom: 1px solid rgba(215, 210, 211, 0.6);
}
</style>
