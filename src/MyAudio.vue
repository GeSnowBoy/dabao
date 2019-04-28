<template>
    <div class="myaudio">
        <i class=" img-icon "
           :class="{stop:isPlay}"
           @click="handleAudio"></i>

    </div>

</template>

<script>
  var audio = new Audio();
  document.body.appendChild(audio);
  export const control = {
    audios: [],
    add(item) {
      this.audios.push(item);
      return item;
    },
    stop() {
      audio.pause();
      this.audios.map(item => {
        item.stop();
      });
    },
    remove(item) {
      this.audios.splice(this.audios.indexOf(item), 1);
    },
    complete() {

    }

  };
  window._control = control
  export default {
    name: 'MyAudio.vue',
    components: {},
    props: {
      src: {
        required: true
      }
    },
    data() {
      return {
        isPlay: false
      };
    },
    mounted() {
      control.add({
        stop: () => {
          this.isPlay = false;
        }
      });
    },
    methods: {
      handleAudio() {
        let isPlay = !this.isPlay;
        control.stop();
        this.isPlay = isPlay;
        if (isPlay) {
          audio.src = this.src;
          audio.play();
          audio.onended = () => {
            this.isPlay = false;
          };
        } else {
          audio.pause();
        }
      }

    },
    computed: {}
  };
</script>

<style scoped lang="less">
    .myaudio {

        display: inline-block;
        position: absolute;
        left: -25px;
        top: 2px;
        width: 20px;
        height: 20px;

        .img-icon {
            display: inline-block;

            width: 20px;
            height: 20px;
            background: url("https://spokenenglishtest.smartedu.lenovo.com/assets/common/images/icon/play.png") no-repeat center center;

            &.stop {
                background: url("https://spokenenglishtest.smartedu.lenovo.com/assets/common/images/icon/stop.png") no-repeat center center;
                background-size: 100%;
            }
        }
    }
</style>
