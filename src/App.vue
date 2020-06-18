<template>
  <div id="app">
    <div class="webcam-container" :style="{ backgroundImage: `url(${currentPhoto})` }">
    </div>
    <div class="overlay-container" v-show="overlayOpen">
      <DataOverlay
        :moisture="moisture"
        :indoor-temp="indoorTemp"
        :outdoor-temp="outdoorTemp"
        :roof-open="roofOpen"
      />
    </div>
    <div class="visibility-icon visible">
      <VisibleIcon v-show="!overlayOpen" v-on:click="showOverlay"/>
      <HiddenIcon v-show="overlayOpen" v-on:click="hideOverlay"/>
    </div>
    <div class="tomato-icon">
      <TomatoIcon v-on:click="showTomatoes"/>
    </div>

  </div>
</template>

<script>
import DataOverlay from './components/DataOverlay.vue'
import VisibleIcon from './assets/visible.svg';
import HiddenIcon from './assets/hidden.svg';
import TomatoIcon from './assets/tomato.svg';

export default {

  name: 'App',
  data: function() {
    return {
      moisture: 0,
      indoorTemp: 0,
      outdoorTemp: 0,
      roofOpen: false,
      overlayOpen: false,
      tomatoOverlay: false,
      currentPhoto: '',
      latestPhoto: '',
      latestOverlay: '',
    }
  },
  components: {
    DataOverlay,
    VisibleIcon,
    HiddenIcon,
    TomatoIcon,
  },
  methods: {
    showOverlay() {
      console.log('show');
      this.overlayOpen = true;
    },
    hideOverlay() {
      console.log('hide');
      this.overlayOpen = false;
    },
    showTomatoes() {
      this.tomatoOverlay = !this.tomatoOverlay;
      if (this.tomatoOverlay) {
        this.currentPhoto = this.latestOverlay;
      } else {
        this.currentPhoto = this.latestPhoto;
      }
    },
  },
  async created() {
    const photoResponse = await fetch(process.env.VUE_APP_PHOTO_URL, {
      headers: new Headers({
        'Authorization': process.env.VUE_APP_PHOTO_URL_AUTH
      }),
    });

    const photoData = await photoResponse.json();
    const photoURL = photoData.latestPhoto;
    this.latestPhoto = photoURL;
    this.currentPhoto = photoURL;
    this.latestOverlay = photoData.latestWithOverlay;

    const response = await fetch(process.env.VUE_APP_DATA_URL, {
      headers: new Headers({
        'Authorization': process.env.VUE_APP_DATA_URL_AUTH
      }),
    });
    const data = await response.json();
    this.overlayOpen = true;
    this.moisture = data['1536020820/humidity'].latestValue;
    this.indoorTemp = data['1536020820/temp'].latestValue;
    this.outdoorTemp = data['1536020830/temp'].latestValue;
    this.roofOpen = data['5890480'].latestValue === 1;
  }
}
</script>

<style lang="scss">
  #app {
    font-family: 'Lexend Tera', sans-serif;
    font-weight: bold;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    height: 100vh;
    position: relative;
    max-width: 900px;
    margin: 0 auto;
  }

  .visibility-icon {
    position: absolute;
    top: 20px;
    right: 20px;
    width: 30px;
    height: auto;
    stroke: white;
    & > svg {
      fill: white;
    }
  }

  .tomato-icon {
    position: absolute;
    top: 60px;
    right: 20px;
    width: 30px;
    height: auto;
    stroke: white;
    & > svg {
      fill: white;
    } 
  }

  .webcam-container {
    background-size: cover;
    background-repeat: no-repeat;
    width: auto;
    height: 100%;
  }

  .overlay-container {
    background: rgba(0, 0, 0, 0.5);
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
</style>
