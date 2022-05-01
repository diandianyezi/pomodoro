<template>
  <div class="my-pomodoro">
    <h1>{{isWorking ? '工作吧' : '休息吧'}}，皮卡丘！</h1>
    <div class="my-pomodoro-clock">
      <div class="my-pomodoro-clock-item minutes">{{ mVal >= 10 ? mVal : '0' + mVal }}</div>
      <div class="my-pomodoro-clock-item seconds">{{ sVal >= 10 ? sVal : '0' + sVal }}</div>
    </div>
    <div class="my-pomodoro-btns">
      <button class="continue" v-if="state === 2"  @click="changeState(1)">继续</button>
      <button class="pause" v-if="state === 1" @click="changeState(2)">暂停</button>
      <button class="pause" v-if="state === 2" @click="changeState(0)">退出</button>
      <button class="start" v-if="state === 0" @click="changeState(1)">{{isWorking ? '开始' : '开始休息'}}</button>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Timer from 'easytimer.js'

const WORK_MINUTES = 1
const WORK_SECONDS = 0

const REST_MINUTES = 1
const REST_SECONDS = 0

export default {
  name: 'Home',
  components: {
  },
  data () {
    return {
      mVal: WORK_MINUTES,
      sVal: WORK_SECONDS,
      isWorking: true,
      timer: 0,
      state: 0 // 0 未开始 1已开始 2已暂停 3已结束
    }
  },
  mounted () {
    this.timer = new Timer({
      startValues: [0, this.sVal, this.mVal, 0, 0],
      target: [0, 0, 0, 0, 0],
      precision: 'seconds',
      countdown: true,
      callback: (localTimer) => {
        const values = localTimer.getTimeValues()
        this.sVal = values.seconds
        this.mVal !== values.minutes && (this.mVal = values.minutes)

        if (this.mVal === 0 && this.sVal === 0) {
          localTimer.stop()
          console.info(this.isWorking ? '结束啦，开始休息' : '开始工作吧')
          // 开始休息倒计时。 播放提示音
          this.isWorking = !this.isWorking
          this.state = 0
        }
      }
    })
    this.timer.addEventListener('stopped', (localTimer) => {
      if (this.sVal === 0 && this.mVal === 0) {
        // 重新开始
        if (this.isWorking) {
          this.mVal = REST_MINUTES
          this.sVal = REST_SECONDS
        } else {
          this.mVal = WORK_MINUTES
          this.sVal = WORK_SECONDS
        }
      }
    })
  },
  methods: {
    changeState (nextState) {
      if (this.state === 0) {
        this.timer.start()
      } else if (this.state === 1) {
        // 暂停
        this.timer.pause()
      } else if (this.state === 2 && nextState === 1) {
        this.timer.start({
          startValues: [
            0,
            this.sVal,
            this.mVal,
            0,
            0
          ]
        })
      } else if (this.state === 2 && nextState === 0) {
        this.timer.pause()
      }
      this.state = nextState
    }
  }
}
</script>
<style lang="less" scoped>
.my-pomodoro {
  // background-color: rgba(0,0,0, 1);
  color: rgba(255,255,255,.8);
  height: 100vh;
  width: 100vw;
  padding-top: 30px;
  box-sizing: border-box;

  h1 {
    text-align: center;
  }

  &-clock {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
    // min-width: 600px;
    &-item {
      border-bottom: 1px solid rgba(255, 255, 255, 0.4);
      border-left: 1px solid rgba(255, 255, 255, 0.4);
      background: linear-gradient(
        to top right,
        rgba(255, 255, 255, 0.25),
        rgba(255, 255, 255, 0.25)
      );
      box-shadow: 10px -10px 20px rgba(0, 0, 0, 0.2),
        -10px 10px 20px rgba(255, 255, 255, 0.1);
      backdrop-filter: blur(5px);
      border-radius: 16px;
      height: 40vw;
      line-height: 40vw;
      text-align: center;
      vertical-align: middle;
      width: 40vw;
      min-width: 200px;
      min-height: 200px;
      font-size: 9rem;
      margin: 10px;
    }
  }

  &-btns {
    display: flex;
    justify-content: center;

    button {
      width: 140px;
      height: 40px;
      line-break: 40px;
      font-size: 16px;
      color: rgba(255,255,255,.9);
      border: none;
      margin: 20px;
      border-radius: 10px;
      border: 1px solid rgba(255,255,255,.7);

      &.pause {
        background: none;
      }
      &.continue {
        background: green;
      }
      &.start {
        background: green;
      }

    }
  }
}
</style>
