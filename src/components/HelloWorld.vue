<template>
  <div style="width: 100%; height: 100%">
    <div id="modal" :class="{ none : bleServer }" @click="initBluetooth('HC-08', '0000ffe0-0000-1000-8000-00805f9b34fb')" ></div>
    <nav>
      <ul>
        <li>
          <a @click="sendMsg(1)" data-item="0">
            <svg version="1.1" fill="#0cf" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(2)" data-item="1">
            <svg version="1.1" fill="rgb(255, 145, 0)" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(3)"  data-item="2">
            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(4)"  data-item="3">
            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(5)"  data-item="4">
            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(6)"  data-item="5">
            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
        <li>
          <a @click="sendMsg(7)"  data-item="6">
            <svg version="1.1" xmlns="http://www.w3.org/2000/svg" x="0px" y="0px" width="64px" height="64px" viewbox="0 0 64 64" enable-background="new 0 0 64 64">
          </svg>
          </a>
        </li>
      </ul>
    </nav>

    <!-- <div @click="initBluetooth('HC-08', '0000ffe0-0000-1000-8000-00805f9b34fb')" class="testBtn" >test btn</div> -->

    <audio id='do' :src="DO" />
    <audio id='re' :src="RE" />
    <audio id='mi' :src="MI" />
    <audio id='fa' :src="FA"/>
    <audio id='sol' :src="SOL"/>
    <audio id='la' :src="LA" />
    <audio id='si' :src="SI" />
  </div>
</template>

<script>
import '../style/index.css'

export default {
  name: "blue-tooth",
  data() {
    return {
      bleServer: null,
      modal: null,
      keyList: ['do', 're', 'mi', 'fa', 'sol', 'la', 'si'],
      DO: require('./music/do.mp3'),
      RE: require('./music/re.mp3'),
      MI: require('./music/mi.mp3'),
      FA: require('./music/fa.mp3'),
      SOL: require('./music/sol.mp3'),
      LA: require('./music/la.mp3'),
      SI: require('./music/si.mp3'),
    };
  },
  mounted() {
  },
  methods: {
    initBluetooth(namePrefix, uuid) {
      document.getElementsByTagName('body')[0].className = 'bleConnect'
      try {
        navigator.bluetooth
        .requestDevice({
          acceptAllDevices: true,
          // filters: [{ namePrefix, }],
          optionalServices: [uuid],
        })
        .then((res) => {
          res.gatt.connect().then((res) => {
            res
              .getPrimaryService(uuid)
              .then((res) => {
                res.getCharacteristics().then((res) => {
                  this.bleServer = res[0];
                  document.getElementsByTagName('body')[0].className = 'bleConnect'
                });
              });
          });
        });
      } catch (ex) {
        console.log(ex)
        alert(ex)
      }
      document.body.requestFullscreen();
    },
    sendMsg(key) {
      if (this.bleServer) {
        this.bleServer.writeValue(new Uint16Array([key]));
        var myAuto = document.getElementById(this.keyList[key - 1]);
            myAuto.play();
      }
      try {
        window.navigator.vibrate([200])
      } catch (ex) {
        console.log(ex)
      }
    },
  }
};
</script>

<style scoped>
.initBle {
  display: flex;
  justify-content: center;
  align-items: center;
}
.bleStatus {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 8px;
  background-color: red;
}
</style>
