<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  width: 100vw;
  height: 100vh;
  margin-top: 30%;
  overflow: hidden;
  transform: scale(0.95);
}

.pointer {
  position: absolute;
  z-index: 2;
}

.roll-pan {
  position: absolute;
  margin-top: 20%;
  z-index: 1;
}

.pm {
  /* position: absolute;
  z-index: 3; */
}

.pm-img {
  position: absolute;
  z-index: 3;
  margin-left: 10%;
  margin-top: 30%;
}

img {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
</style>

<template>
  <div id="app">
    <div class="wrapper">
      <div class="roll-area">
        <div class="pointer">
          <img
            src="../static/point.png"
            alt=""
            style="margin-right: 20%;"
            width="80%"
          >
        </div>
        <div class="roll-pan">
          <img
            id="wheelTable"
            src="../static/rollpan.png"
            style="margin-right: 20%;"
            alt=""
            width="80%"
          >
        </div>
      </div>
      <div class="pm">
        <img
          class="pm-img"
          src="../static/pm.png"
          alt=""
          width="40%;"
          @click="Rolling()"
        >
      </div>
    </div>
  </div>
</template>

<script>
import jQueryRotate from "../static/utils/jQueryRotate";

export default {
  name: 'App',
  data() {
    return {
      Rotating: false
    }
  },
  methods: {
    Rolling() {
      let self = this
      let randomNum = Math.floor((Math.random() * 5))
      if (self.Rotating) {
        return
      }
      self.Rotating = true
      let count = 5;
      // 应该旋转的角度，旋转插件角度参数是角度制。
      var baseAngle = 360 / count;
      // 旋转角度 == 270°（当前第一个角度和指针位置的偏移量） - 奖品的位置 * 每块所占的角度 - 每块所占的角度的一半(指针指向区域的中间)
      let angles = 300 * 3 / 5 - randomNum * baseAngle - baseAngle / 2; // 因为第一个奖品是从0°开始的，即水平向右方向
      $("#wheelTable").stopRotate();
      // 注意，jqueryrotate 插件传递的角度不是弧度制。
      // 哪个标签调用方法，旋转哪个控件
      $("#wheelTable").rotate({
        angle: 0,
        animateTo: angles + 360 * 10, // 这里多旋转了5圈，圈数越多，转的越快
        duration: 8000,
        callback: function () {
          self.Rotating = false;
        }
      });
    },
  },
  beforeMount() {
    let self = this
    self.$axios.get('https://cm.whatyouneed.cc/api/v1/wxauth')
      .then(function (response) {
        console.log(response)
        if (response.data.status === 200) {
          wx.updateTimelineShareData({
            title: store.state.wxUserInfos.nickname + '给你赠送了一个娃娃，快来夹住 TA', // 分享标题
            link: self.baseUrl + '/index.html#/?activity=' + self.activityId + '&openid=' + store.state.wxUserInfos.openid + '&where=timeline', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
            imgUrl: 'http://blog.image.whatyouneed.cc/sharecover@2x.png', // 分享图标
            success: function () {
            }
          })
          wx.updateAppMessageShareData({
            title: store.state.wxUserInfos.nickname + '给你赠送了一个娃娃，快来夹住 TA', // 分享标题
            desc: '爱要抓住，喜欢就要夹住', // 分享描述
            link: self.baseUrl + '/index.html#/?activity=' + self.activityId + '&openid=' + store.state.wxUserInfos.openid + '&where=timeline', // 分享链接，该链接域名或路径必须与当前页面对应的公众号JS安全域名一致
            imgUrl: 'http://blog.image.whatyouneed.cc/sharecover@2x.png', // 分享图标
            success: function () {
            }
          })
        }
      })
  }
}
</script>
