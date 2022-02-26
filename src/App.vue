<script setup>
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://v3.vuejs.org/api/sfc-script-setup.html#sfc-script-setup
import Background from './components/Background.vue'
import HeaderLogo from './components/HeaderLogo.vue'
import InfoText from './components/InfoText.vue'
import EntryBox from './components/EntryBox.vue'
import Globe from './components/Globe.vue'
</script>

<script>
const API_URL = 'https://localhost:3000'

const STAGES = {
  IDLE: 'idle',
  LOADING: 'loading',
  COMPLETED: 'completed',
  ERRORED: 'errored'
}

export default {
  data() {
    return {
      stage: STAGES.IDLE
    }
  },
  methods: {
    async sendMessageHandler(data) {
      const [message, type] = data
      // Open loading modal after 1000 ms
      await this.sleep(1000)
      this.askAI({
        message, type
      })
    },
    async askAI(body) {
      this.stage = STAGES.LOADING
      
      let request = fetch(API_URL, {
        method: 'POST',
        body: JSON.stringify(body)
      }).then(response => {
        response.json()
      })
      .catch(err => {
        console.error(err)
      })

      await Promise.all([request, this.sleep(5000)])
      .then(json => {
        // Do things with response
        this.stage = STAGES.COMPLETED
      })
      .catch(err => {
        // Do things with error
        this.stage = STAGES.ERRORED
        console.error(error)
      })
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms))
    }
  }
}
</script>

<template>
  <Background />
  <Globe :autoRotateSpeed="3" :isLoading="stage === 'loading'" />
  <HeaderLogo />
  <InfoText />
  <EntryBox @sendMessage="sendMessageHandler"/>
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
</style>
