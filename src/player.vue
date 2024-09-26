<template>
  <div class="player">
    <div id="container" style="width: 600px; height: 400px"></div>
    <div id="container-fullscreen"></div>

    <div>
      <button v-on:click="initEzopen">ezopen</button>
      <button v-on:click="initFlv">flv</button>
      <button v-on:click="initHls">hls</button>
    </div>
    <div>
      <button v-on:click="init">init</button>
      <button v-on:click="stop">stop</button>
      <button v-on:click="play">play</button>
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
  props: {
    msg: String,
  },
  mounted: () => {},
  data: () => {
    return { player: null };
  },
  methods: {
    initEzopen() {
      this.destroy();
      this.player = this.ezopenPlayer();
      this.player.play();
    },
    initFlv() {
      this.destroy();
      this.player = this.flvPlayer();
      this.player.play();
    },
    initHls() {
      this.destroy();
      this.player = this.hlsPlayer();
      this.player.play();
    },
    ezopenPlayer() {
      return new EZUIKit.EZUIKitPlayer({
        id: "container",
        width: 600,
        height: 400,
        template: "simple",
        staticPath: "/ezuikit_static",
        ...playerUrlList["ezopen"],
      });
    },
    flvPlayer() {
      return new EzuikitFlv({
        id: "container", // support element id
        width: 600,
        height: 400,
        staticPath: "/flv_decoder/", // 自定义解码库加载地址， 默认放置在服务器根目录下
        ...playerUrlList["flv"],
      });
    },
    hlsPlayer() {
      return new HlsPlayer({
        id: "container",
        staticPath: "/hls_decoder/", // decoder静态资源文件夹 默认根目录
        width: 600,
        height: 400,
        ...playerUrlList["hls"],
      });
    },

    init() {
      if (this.player) {
        this.player.destroy();
        this.player = null;
      }

      this.player = this.flvPlayer();
      this.player.play();
    },

    play() {
      this.player.play();
    },
    stop() {
      this.player.pause();
    },
    fullScreen() {
      if (this.player.fullscreen) {
        this.player.fullscreen();
      } else {
        this.player.fullScreen?.();
      }
    },
    destroy() {
      if (this.player) {
        this.player.destroy();
        this.player = null;
      }
    },
  },
};
</script>
