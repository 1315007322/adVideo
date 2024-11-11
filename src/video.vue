<template>
  <div>
    <video id="dialog-video" width="100%" autoplay v-if="url" playsinline="true" webkit-playsinline="true" style='height:400px'>
      <source :src="url" type="video/mp4" />
      您的浏览器不支持视频标签。
    </video>
    <p id="timer-text" style='position:absolute;top:13px;left:0;right:0;margin:auto;color:red;font-weight: bold'>倒计时：{{ timer }} 秒</p>
  </div>
</template>
<script>
import { watch } from "vue";
export default {
  props: {
    timer: String,
    url: String,
  },
  setup(props) {
    watch(props.timer, (newValue) => {
      console.log(newValue);
    });
  },
};
</script>
