<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import Background from './components/Background.vue'
import HeaderLogo from './components/HeaderLogo.vue'
import InfoText from './components/InfoText.vue'
import EntryBox from './components/EntryBox.vue'
import Globe from './components/Globe.vue'
import ResultOverlay from './components/ResultOverlay.vue'
</script>

<script>
const API_URL = 'https://us-west2-ukrai-342604.cloudfunctions.net/curie223'

const STAGES = {
  IDLE: 'idle',
  LOADING: 'loading',
  COMPLETED: 'completed',
  ERRORED: 'errored'
}

const CLASSIFICATIONS = {
  MISLEADING: 'misleading',
  NOT_MISLEADING: 'not_misleading',
  UNKNOWN: 'unknown'
}

export default {
  data() {
    return {
      stage: STAGES.IDLE,
      classification: CLASSIFICATIONS.UNKNOWN,
      content: 'Content awareness',
      percent: 0.75,
    }
  },
  methods: {
    async sendMessageHandler(data) {
      const [payload, type] = data
      // Open loading modal after 1000 ms
      await this.sleep(1000)
      this.stage = STAGES.LOADING
      this.askAI({
        payload, type
      })
    },

    async askAI(body) {
      console.log('updated stage', this.stage)
      
      const myHeaders = new Headers();
      myHeaders.append("Content-Type", "application/json");

      console.log('Sending', body)
      const raw = JSON.stringify(
        body
      );

      const requestOptions = {
        method: 'POST',
        headers: myHeaders,
        body: raw,
        redirect: 'follow'
      };

      const request = fetch(API_URL, requestOptions)
      .then(response => response.json()).catch(err => {
        this.content = ''
        this.percent = 0
        this.classification = CLASSIFICATIONS.UNKNOWN
        console.error(err)
      })

      await Promise.all([request, this.sleep(5000)])
      .then(res => {
        let [json, _sleep] = res
        // Do things with response
        this.content = json.content || ''
        this.classification = json.classification || ''
        this.percent = json[json.classification] || 0
        this.stage = STAGES.COMPLETED
      })
      .catch(err => {
        // Do things with error
        this.stage = STAGES.ERRORED
        this.content = ''
        this.percent = 0
        this.classification = CLASSIFICATIONS.UNKNOWN
        console.error(err)
      })
    },
    handleCompleted() {
      this.stage = STAGES.IDLE
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  },
  computed: {
    parsedText() {
      return this.content.replace(/  +/g, ' ')
    },
    isLoading() {
      return this.stage === STAGES.LOADING
    },
    isCompleted() {
      return this.stage === STAGES.COMPLETED
    }
  }
}
</script>

<template>
  <Background />
  <Globe :autoRotateSpeed="3" :isLoading="isLoading" />
  <HeaderLogo />
  <InfoText />
  <EntryBox @sendMessage="sendMessageHandler"/>
  <ResultOverlay v-if="isCompleted" :type="classification" @completed="handleCompleted" :content="parsedText" :percent="percent" />
</template>

<style>
html {
  background-color: var(--primary);
  margin: 0;
  padding: 0;
}

body {
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Manrope', Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: absolute;
  width: 100%;
  height: 100vh;
  
  overflow: hidden;
  background-color: rgba(0,0,0,0);
}

:root {
  --primary: #3502FF;
}

@keyframes fadeIn {
  0% { opacity: 0; top: calc(50% - 40px) }
  100% {opacity: 1; top: 50%; }
}
</style>
