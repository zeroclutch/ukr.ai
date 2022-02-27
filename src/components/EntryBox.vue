<script>
const ENTRIES = {
  INVALID: 0,
  TWEET: 1,
  TEXT: 2
}
export default {
  data() {
    return {
      entry: '',
      isSending: false
    }
  },
  methods: {
    sleep(ms) {
      return new Promise(resolve => {
        setTimeout(resolve, ms)
      })
    },
    async postMessage() {
      this.isSending = true

      // Attempt message if valid
      if(this.validateMessage !== ENTRIES.INVALID) {
        await Promise.all([this.sendMessage(this.$data.entry), this.sleep(4000)])
      }
      this.isSending = false
    },
    async sendMessage(message) {
      // Post to API server
      this.$emit('sendMessage', [ message, this.type ])
      return false
    }
  }, computed: {
    ENTRIES() { return ENTRIES },
    type() {
      // Ensure message is valid link or reasonable text
      if(this.entry.includes('twitter.com')) return ENTRIES.TWEET
      else if(this.entry.startsWith('https://')) return ENTRIES.INVALID
      else return ENTRIES.TEXT
    },
  }
}
</script>

<template>
  <div class="entry-box-wrapper">
    <textarea v-model="entry" @keydown.enter="e => { e.preventDefault(); this.postMessage() }" type="text" maxlength="140" class="entry-box" placeholder="Start with a news headline, twitter link, or piece a of information."></textarea>
    <button @click="postMessage" class="submit-button" :class="{
      'is-twitter': type === ENTRIES.TWEET,
      'is-text': type === ENTRIES.TEXT || type === ENTRIES.INVALID
     }"
     :disabled="type === ENTRIES.INVALID">
     <span class="rocket-wrapper" :class="{ 'rocket-animation': isSending }" @click="postMessage">
       <img aria-label="Paper Airplane icon" src="../assets/paper-plane.png">
      </span>
    </button>
  </div>
</template>

<style scoped>
.entry-box-wrapper {
  position: relative;
  left: 0;
  margin: 40px 0;
  width: 100%;
  z-index: 10;
}

.entry-box {
  font-family: 'Manrope', Avenir, Helvetica, Arial, sans-serif;
  min-width: 100px;
  width: 90vw;
  height: 48px;
  max-width: 400px;
  padding: 18px;
  border: none;
  top: 0;
  box-shadow: 0px 4px 4px 0px rgba(0, 0, 0, 0.251);
  border-radius: 12px;
  font-size: 16px;
}

button.submit-button {
  border: none;
  display: block;
  padding: 8px;
  position: relative;
  margin: 0 auto;
  top: -30px;
  left: 100px;
  width: 80px;
  height: 45px;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  border-radius: 12px;
  overflow: hidden;
  text-align: center;
  transition: all 300ms;
}

button.submit-button.is-text {
  background: #F94970D9;
  box-shadow: 0px 0px 8px 0px rgba(249, 73, 112, 0.851);
}

button.submit-button.is-twitter {
  background: #49CEF9D9;
  box-shadow: 0px 0px 8px 0px rgba(73, 206, 249, 0.85);;
}

button.submit-button img {
  height: 26px;
  margin: 2px 2px 0 0;
}

.rocket-animation {
  position: absolute;
  transform: translate(-50%, 50%);
  left: 50%;
  bottom: 50%;
  animation: rocket-animation 4s ease-in-out 1 forwards;
}

@keyframes rocket-animation {
  /* Initial shake */
  0% { left: 50%; bottom: 50%; }
  1% { left: 53%; bottom: 53%; }
  2% { left: 48%; bottom: 48%; }
  3% { left: 50%; bottom: 48%; }
  4% { left: 48%; bottom: 53%; }
  5% { left: 50%; bottom: 53%; }
  6% { left: 50%; bottom: 50%; }
  7% { left: 53%; bottom: 53%; }
  8% { left: 48%; bottom: 48%; }
  9% { left: 50%; bottom: 48%; }
  10% { left: 48%; bottom: 53%; }
  11% { left: 50%; bottom: 53%; }
  12% { left: 48%; bottom: 48%; }
  13% { left: 50%; bottom: 48%; }
  14% { left: 48%; bottom: 53%; }
  15% { left: 50%; bottom: 53%; }
  16% { left: 50%; bottom: 50%; }
  17% { left: 53%; bottom: 53%; }
  18% { left: 48%; bottom: 48%; }
  19% { left: 50%; bottom: 48%; }
  /* Recenter and aim for top right */
  20% { left: 50%; bottom: 50%; }
  40% { left: 150%; bottom: 150%; }
  41% { left: 200%; bottom: 200%; }
  /* Return to bottom left */
  42% { left: 200%; bottom: -200%; }
  43% { left: -200%; bottom: -200%; }
  70% { left: 50%; bottom: 50%; }
  100% { left: 50%; bottom: 50%; }
}
</style>
