<template>
  <div class="player">
    <div id="container"></div>
    <div id="container-fullscreen"></div>
    <div class="btn-wrapper">
      <button v-on:click="initEzopenPlayer">ezopen player 私有流</button>
      <button v-on:click="initFlvPlayer">flv player 标准流</button>
      <button v-on:click="initHlsPlayer">hls player 标准流</button>
    </div>
    <div>
      <button v-on:click="play">play</button>
      <button v-on:click="pause">pause</button>
      <button v-on:click="fullScreen">fullScreen</button>
      <button v-on:click="destroy">销毁</button>
    </div>
  </div>
</template>

<script>
import EZUIKit from "ezuikit-js";
import EzuikitFlv from "ezuikit-flv";
import HlsPlayer from "@ezuikit/player-hls";

const playerUrlList = {
  ezopen: {
    url: "ezopen://open.ys7.com/BC7900686/1.hd.live",
    accessToken:
      "at.4uj94pyi7bxbun124f8zu2y10lwi8vh6-45hn5calrq-1l00y6t-hxmutic36",
  },
  flv: {
    url: "https://rtmp01open.ys7.com:9188/v3/openlive/BC7900686_1_1.flv?expire=1758455431&id=759502184183341056&t=0fe3760021dafcf9d139395297a2c1f87927be456f3ebded69a38874aed2d317&ev=100", // https://play.com/9999.flv
  },
  hls: {
    url: "https://open.ys7.com/v3/openlive/BC7900686_1_1.m3u8?expire=1758455431&id=759502184155979776&t=9fd1ec941bee0b781074655ea0621ed3cf8b675614a244dfb1e1e7753c652fc9&ev=100",
  },
};

export default {
  name: "player",
  props: {},
  mounted: () => {},
  data: () => {
    return { player: null };
  },
  methods: {
    /** 初始化 ezopen 协议私有流 */
    initEzopenPlayer() {
      this.destroy();
      // 默认自动播放
      this.player = this.initPlayer(playerUrlList["ezopen"]);
    },
    /** 初始化 flv 标准流 */
    initFlvPlayer() {
      this.destroy();
      this.player = this.initPlayer(playerUrlList["flv"]);
      this.player.play();
    },
    /** 初始化 hls 标准流 */
    initHlsPlayer() {
      this.destroy();
      this.player = this.initPlayer(playerUrlList["hls"]);
      this.player.play();
    },

    /**
     * @description  ezopen  flv hls 播放器 三合一， 根据播放地址判断使用那一个播放器
     * @param {object} options
     * @param {string} options.url
     * @param {string=} options.accessToken
     */
    initPlayer(options = {}) {
      const width = window.screen.width
      const height = width * (9 /16)

      if (/^ezopen:\/\//.test(options.url)) {
        return new EZUIKit.EZUIKitPlayer({
          id: "container",
          width: width,
          height: height,
          template: "simple",
          staticPath: "/ezuikit_static",
          ...options,
        });
      } else if (options.url.includes(".flv")) {
        // FIXME: 这是一个bug 后期会修复
        document.getElementById("container").style.width = width + 'px'
        document.getElementById("container").style.height = height + 'px'
 
        return new EzuikitFlv({
          id: "container", // support element id
          width: width,
          height: height,
          staticPath: "/flv_decoder/", // 自定义解码库加载地址， 默认放置在服务器根目录下
          ...options,
        });
      } else if (options.url.includes(".m3u8")) {
        return new HlsPlayer({
          id: "container",
          staticPath: "/hls_decoder/", // decoder静态资源文件夹 默认根目录
          width: width,
          height: height,
          ...options,
        });
      }
      throw new Error("不支持播放地址");
    },


    /** 播放 */
    play() {
      if (this.player) {
        this.player.play();
      }
    },
    /** 暂停 */
    pause() {
      if (this.player) {
        this.player.pause();
      }
    },
    /** 全屏 移动端H5 不支持 （因为 H5 不能使用快捷键退出） */
    fullScreen() {
      if (this.player) {
        if (this.player.fullscreen) {
          this.player.fullscreen();
        } else {
          // ezopen 协议 
          // FIXME: 后面版本会统一
          this.player.fullScreen?.();
        }
      }
    },
    /** 销毁 */
    destroy() {
      if (this.player) {
        this.player.destroy();
        this.player = null;
      }
    },
  },
};
</script>

<style scoped>
.btn-wrapper {
  padding-top: 20px;
}
</style>
