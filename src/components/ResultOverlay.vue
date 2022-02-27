<script>
const TYPES = {
    'not_misleading': 'Not misleading.',
    'misleading': 'Misleading.',
    'unknown': 'Unknown.',
}

export default {
    props: {
        type: String,
        percent: Number,
        content: String,
    },
    computed: {
        dashOffset() {
            // Max: 340
            // Min: 500
            let MAX = 348;
            let MIN = 500;
            return ((MAX - MIN) * this.percent + MIN) + 'px'
        },
        cleanPercent() {
            return Math.round(this.percent * 100)
        },
        cleanType() {
            return TYPES[this.type]
        },
        getColorFromType() {
            switch(this.type) {
                case 'not_misleading':
                    return '#18C672'
                case 'misleading':
                    return '#D4072C'
                default:
                    return '#C6A018' // F8D96D
            }
        }
    }
}
</script>

<template>
    <div class="result-overlay-background active" @click="this.$emit('completed')">
    </div>
    <div
    class="result-modal"
    :class="{
        'not-misleading': this.type === 'not_misleading',
        'misleading':     this.type === 'misleading',
        'unknown':        this.type !== 'not_misleading' && this.type !== 'misleading'
    }">
        <div class="result-percent">
            <div class="result-percent-wrapper">
                <svg width="60" height="60" viewBox="0 0 60 60" fill="none" xmlns="http://www.w3.org/2000/svg">
                    <circle class="percent-holder" cx="30" cy="30" r="30" fill="#FFFFFF" stroke-width="0"/>
                    <circle
                        class="percent-graph" cx="30" cy="30" r="24" stroke-linecap="round" :stroke="getColorFromType"
                        :stroke-dashoffset="this.dashOffset" stroke-width="5"/>
                </svg>
            </div>
            <div class="result-percent-text" :style="{ color: getColorFromType }">{{ cleanPercent }}%</div>
        </div>
        <div class="result-classification">{{ cleanType }}</div>
        <div class="result-quote">
            {{ content ? '&ldquo;' : '' }}{{ content || `We couldn't find anything for this.` }}{{ content ? '&rdquo;' : '' }}
        </div>
        <div class="result-footer">
            Having an issue with this result?<br>I'm still learning! Report it <a href="https://forms.gle/rDhdFSdwzV1QAmVLA" aria-label="Issue report form">here</a>.
        </div>
    </div>
</template>

<style scoped>
.result-overlay-background {
    display: block;
    position: absolute;
    width: 100vw;
    height: 100vh;
    top: 0;
    left: 0;
    opacity: 1;
    z-index: 50;
    background-color: rgba(0, 0, 0, 0.55);
}

.result-modal {
    position: absolute;
    z-index: 50;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    color: white;

    border-radius: 14px;
    padding: 16px;
    box-shadow: 0px 4px 10px rgba(0, 0, 0, 0.5);
    font-weight: 600;

    width: 350px;
    min-height: 200px;

    animation: fadeIn 0.3s ease-out;

    backdrop-filter: blur(20px);
    -webkit-backdrop-filter: blur(20px);
    
}

.result-modal.misleading {
    background: rgba(212, 7, 44, 0.7);
}

.result-modal.not-misleading {
    background: rgba(7, 212, 114, 0.7);
}

.result-modal.unknown {
    background: rgba(198, 160, 24, 0.7);
}

.result-footer {
    font-size: 11px;
    color: white;
    padding: 5px;
}

.result-footer a {
    color: white;
}

.result-quote {
    font-size: 18px;
    padding: 5px;
}

.result-percent {
    position: relative;
    /* stroke-dashoffset: 500; */
    stroke-dasharray: 500;
    animation: dash 0.4s linear forwards;
}

.result-percent-wrapper {
    transform: rotate(-90deg);
}

.result-percent-text {
    font-size: 16px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}

.result-classification { 
    font-size: 32.25px;
    padding: 5px;
}
</style>