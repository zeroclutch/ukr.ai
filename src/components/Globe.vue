<script>
import * as THREE from 'three'
import Globe from 'globe.gl'

let world

export default {
  props: {
    autoRotateSpeed: Number,
    isLoading: Boolean,
  },
  data() {
    return {
      loadingMessages: [
        'Connecting to AI...',
        'Loading natural language model...',
        'Churning over curated datasets...',
        'Identifying relevant documents...',
        'Drawing inferences...',
        'Developing conclusions...',
        'Still thinking...',
        'Working on it...',
      ],
      loadingMessage: 'Loading...',
    }
  },
  methods: {
    toggleLoading() {
      this.loading = !this.loading
      if(this.loading) {
        this.displayLoadingMessages()
      }
    },
    async typeText(str) {
      this.loadingMessage = ''
      for(let character of str) {
        if(!this.loading) return false
        this.loadingMessage += character
        await this.sleep(Math.round(Math.random() * 120))
      }
      return true
    },
    async displayLoadingMessages() {
      for(let message of this.loadingMessages) {
        await this.typeText(message)
        if(!this.loading) return false
        await this.sleep(2000)
      }
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  },
  mounted() {
    const ARC_REL_LEN = 0.4; // relative to whole arc
    const FLIGHT_TIME = 1000;
    const NUM_RINGS = 3;
    const RINGS_MAX_R = 5; // deg
    const RING_PROPAGATION_SPEED = 5; // deg/sec

    world = Globe()
    .globeImageUrl('/earth-light.jpeg')
    .bumpImageUrl('https://unpkg.com/three-globe/example/img/earth-topology.png')
    .backgroundColor('#00000000')
    .height('600px')
    .arcColor(() => '#2200A8')
    .arcDashLength(ARC_REL_LEN)
    .atmosphereColor('#2200A8')
    .onGlobeClick(emitArc)
    .arcDashGap(2)
    .arcDashInitialGap(1)
    .arcDashAnimateTime(FLIGHT_TIME)
    .arcsTransitionDuration(0)
    .ringColor(() => t => `rgba(34, 0, 168,${1-t})`)
    .ringMaxRadius(RINGS_MAX_R)
    .ringPropagationSpeed(RING_PROPAGATION_SPEED)
    .ringRepeatPeriod(FLIGHT_TIME * ARC_REL_LEN / NUM_RINGS)
    (document.getElementById('globe-visualization'));

    const controls = world.controls()

    // custom globe material
    const globeMaterial = world.globeMaterial();
    globeMaterial.bumpScale = 20;
    new THREE.TextureLoader().load('https://unpkg.com/three-globe/example/img/earth-water.png', texture => {
      globeMaterial.specularMap = texture;
      globeMaterial.specular = new THREE.Color('grey');
      globeMaterial.shininess = 5;
    });

    controls.autoRotate = true
    controls.enableZoom = false
    controls.autoRotateSpeed = this.autoRotateSpeed

    let prevCoords = { lat: 0, lng: 0 };
    setTimeout(() => {
      const scene = world.scene()
      const light = scene.children.find(obj3d => obj3d.type === 'DirectionalLight');
      light.intensity = 0.3
      light.position.set(0, 2, 2)
      light.castShadow = true
    }, 1)

    setInterval(() => {
      emitArc({ lat: Math.random() * 1000, lng: Math.random() * 1000 })
    }, 200)
    
    function emitArc({ lat: endLat, lng: endLng }) {
      const { lat: startLat, lng: startLng } = prevCoords;
      setTimeout(() => { prevCoords = { lat: endLat, lng: endLng }}, FLIGHT_TIME);

      // add and remove arc after 1 cycle
      const arc = { startLat, startLng, endLat, endLng };
      world.arcsData([...world.arcsData(), arc]);
      setTimeout(() => world.arcsData(world.arcsData().filter(d => d !== arc)), FLIGHT_TIME * 2);

      // add and remove start rings
      const srcRing = { lat: startLat, lng: startLng };
      world.ringsData([...world.ringsData(), srcRing]);
      setTimeout(() => world.ringsData(world.ringsData().filter(r => r !== srcRing)), FLIGHT_TIME * ARC_REL_LEN);

      // add and remove target rings
      setTimeout(() => {
        const targetRing = { lat: endLat, lng: endLng };
        world.ringsData([...world.ringsData(), targetRing]);
        setTimeout(() => world.ringsData(world.ringsData().filter(r => r !== targetRing)), FLIGHT_TIME * ARC_REL_LEN);
      }, FLIGHT_TIME);
    }

    window.addEventListener('resize', (event) => {
      world.width([event.target.innerWidth])
      world.height([event.target.innerHeight])
    });
  }
}

</script>

<template>
  <div class="globe-overlay-background" :class="{ 'active': isLoading }"></div>
  <div class="globe-overlay" :class="{ 'active': isLoading }">
    <h1 class="loading-text">
      <span class="loading-highlight">{{ loadingMessage.split(' ')[0] }}</span>
      {{ loadingMessage.split(' ').slice(1).join(' ') }}
    </h1>
  </div>
  <div id="globe-visualization" :class="{ 'loading': isLoading }">Your browser does not have WebGL enabled.</div>
  
</template>

<style scoped>
  .globe-overlay-background {
    position: absolute;
    display: none;
    width: 100vw;
    height: 100vh;
    opacity: 0;
    z-index: 50;
    background-color: rgba(0, 0, 0, 0.55);
  }

  .globe-overlay-background.active {
    display: block;
    opacity: 1;
  }

  .globe-overlay {
    position: absolute;
    display: none;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    overflow: visible;

    width: calc(100vw - 80px);
    max-width: 500px;
    height: calc(100vh - 80px);
    max-height: 600px;

    background-color: #ffffffdd;
    backdrop-filter: blur(10px);
    -webkit-backdrop-filter: blur(10px);
    border-radius: 12px;
    
    opacity: 0;
  }

  .globe-overlay.active {
    display: block;
    top: 50%;
    animation: fadeIn 0.3s ease-out;
    opacity: 1;
    z-index: 50;
  }

  .loading-text {
    margin-top: 50px;
    text-align: left;
    padding: 50px;
  }

  .loading-text .loading-highlight {
    color: var(--primary);
  }

  #globe-visualization {
    background-color: #00000000;
    position: absolute;
    transform: scale(1) translate(-50%, 50%);
    transition: transform 0.5s;
    bottom: 0;
    left: 50%;
    z-index: 21;
  }

  #globe-visualization.loading {
    transform: scale(0.5, 0.5) translate(-100%, 25%);
    left: 50%;
    z-index: 55;
  }

</style>
