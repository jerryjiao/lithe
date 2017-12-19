<template>
  <div class="hello">
    <div class="log-img">
      <img src="../assets/img/logo.png" alt="">
    </div>
    <div>
      <vue-circle
        :progress="value*2"
        :size="200"
        :reverse="false"
        line-cap="round"
        :fill="fill"
        empty-fill="rgba(0, 0, 0, .1)"
        :animation-start-value="0.0"
        :start-angle="1.6"
        insert-mode="append"
        :thickness="10"
        :show-percent="false"
        ref="myCircleID">
        <p class="showNumber">{{showValue}}</p>
      </vue-circle>

    </div>
    <div class="sliderPart" v-if="isStart">
      <vue-slider 
      v-model="value"
      :width = "300"
      :height = "8"
      :dot-size = "20"
      :min = "5"
      :max = "50"
      :interval = "5"
      formatter = '{value} mins'
      piecewise
      lazy
      >
      </vue-slider>
    </div> 
    <button class="start-button" @click="start()" v-if="isStart">{{buttonText}}</button>
    <div v-else>
      <div><button class="start-button" @click="start()">Restart</button></div>
      <div><button class="start-button buttonRelax" @click="relax()" v-if="isRelax" :disabled="isRelaxButton">Relax!</button></div>  
    </div>
    <div>
      <audio src="/static/audio/2.mp3" id="alertAudio"></audio>
    </div>
    <div class="help">
      ?
    </div>
  </div>
</template>

<script>
import vueSlider from 'vue-slider-component'
import VueCircle from 'vue2-circle-progress'
import moment from 'moment'

export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      value: 25,
      buttonText: 'Start',
      fill: { gradient: ['red', 'blue'] },
      showValue: moment().minute(25).second(0).format('mm:ss'),
      isStart: true,
      isRelax: false,
      isRelaxButton: false,
      myVar: null,
      myVar2: null
    }
  },
  components: {
    vueSlider, VueCircle
  },
  methods: {
    start () {
      let _this = this
      _this.$refs.myCircleID.updateFill(_this.fill)
      _this.cleanTimer()
      _this.isRelax = false
      _this.isRelaxButton = false
      Notification.requestPermission(function (status) {
        if (Notification.permission !== status) {
          Notification.permission = status
        }
        var n = new Notification('Hi! ', {
          tag: 'Beyoung',
          icon: 'http://ihuster.com/static/avatar/b_default.png',
          body: 'Start Wordking!'
        })
        n.onshow = function () {
          console.log('Start Wordking!')
        }
      })
      if (_this.isStart) {
        _this.isStart = false
        let timenow = moment().minute(_this.value).second(0)
        _this.myVar = setInterval(function () {
          if (_this.showValue !== '00:00') {
            timenow = timenow.subtract(1, 's')
            _this.showValue = timenow.format('mm:ss')
            _this.value = timenow.format('mm')
          } else {
            _this.playAudio()
            _this.isRelax = true
            _this.cleanTimer()
            Notification.requestPermission(function (status) {
              if (Notification.permission !== status) {
                Notification.permission = status
              }
              var n = new Notification('Hi! ', {
                tag: 'Beyoung',
                icon: 'http://ihuster.com/static/avatar/b_default.png',
                body: 'Start Relax!'
              })
              n.onshow = function () {
                console.log('Start Relax!')
              }
            })
          }
        }, 1000)
      } else {
        _this.isStart = true
        _this.cleanTimer()
        _this.showValue = moment().minute(25).second(0).format('mm:ss')
        _this.value = 25
      }
    },
    moment: function () {
      return moment()
    },
    relax () {
      let _this = this
      _this.$refs.myCircleID.updateFill('#00FF99')
      _this.isRelaxButton = true
      // clearInterval(_this.myVar)
      _this.cleanTimer()
      _this.showValue = moment().minute(5).second(0).format('mm:ss')
      _this.value = 5
      let timenow = moment().minute(_this.value).second(0)
      _this.myVar2 = setInterval(function () {
        if (_this.showValue !== '00:00') {
          timenow = timenow.subtract(1, 's')
          _this.showValue = timenow.format('mm:ss')
          _this.value = timenow.format('mm')
        } else {
          _this.playAudio()
          _this.$refs.myCircleID.updateFill(_this.fill)
          _this.isStart = true
          _this.cleanTimer()
          _this.showValue = moment().minute(25).second(0).format('mm:ss')
          _this.value = 25
          Notification.requestPermission(function (status) {
            if (Notification.permission !== status) {
              Notification.permission = status
            }
            var n = new Notification('Hi! ', {
              tag: 'Beyoung',
              icon: 'http://ihuster.com/static/avatar/b_default.png',
              body: 'End Relax!'
            })
            n.onshow = function () {
              console.log('End Relax!')
            }
          })
        }
      }, 1000)
    },
    cleanTimer () {
      clearInterval(this.myVar)
      clearInterval(this.myVar2)
    },
    playAudio () {
      var audio = document.getElementById('alertAudio')
      audio.play()
    }
  },
  watch: {
    value (curVal, oldVal) {
      this.$refs.myCircleID.updateProgress(curVal * 2)
      if (!this.isStart) {
        if (Math.abs(curVal - oldVal) > 1) {
          this.showValue = moment().minute(curVal).second(0).format('mm:ss')
        } else {
          this.showValue = moment().minute(curVal).second(59).format('mm:ss')
        }
      } else {
        this.showValue = moment().minute(curVal).second(0).format('mm:ss')
      }
    }
  },
  filters: {
    moment: function (data) {
      if (typeof (data) === 'string') {
        return data
      } else {
        return moment().minute(data).second(0).format('mm:ss')
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.log-img {
  img {
    width: 130px;
  }
  margin-bottom: 30px;
}
.vue-slider-component {
  margin: 0 auto;
}
.sliderPart {
  margin-top:50px;
  height: 28px;
}
.start-button {
    margin-top: 30px;
    padding: 10px 50px;
    color: #fff;
    background-color: #0275d8;
    // border-color: #0275d8;
    border: none;
  }
.buttonRelax {
  background-color: #00FF99;
  padding: 10px 54px;
}
.showNumber {
  font-size: 30px;
}
.help {
  width: 25px;
  height: 25px;
  line-height: 25px;
  color: gray;
  // font-size: 14px;
  border: 1px solid gray;
  border-radius: 50%;
  cursor: pointer;
  text-align: center;
}
</style>
