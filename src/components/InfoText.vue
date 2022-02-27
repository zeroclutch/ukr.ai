<script>

</script>

<template>
  <h1 class="info-box">
    Post a <div class="highlight-wrapper"><span class="highlight highlight-text">fact</span></div> or <div class="highlight-wrapper"><span class="highlight highlight-twitter">Twitter</span></div> link about the Russia-Ukraine conflict. Our AI will tell you if it's misleading.
  </h1>
</template>

<style scoped>

.info-box {
  color: white;
  font-size: 27.34px;
  line-height: 37.35px;
  font-weight: 800;
  padding: 10px 50px;
  text-align: left;
  max-width: 450px;
  margin: 0 auto;
}

.highlight-wrapper {
  display: inline-block;
  position: relative;
}

.highlight-text:not(::before) {
  position: relative;
  z-index: 2;
}

.highlight::before {
  display: inline-block;
  position: absolute;
  top: 26px;
  left: 0px;
  height: 8px;
  content: "";
  z-index: -1;
}

.highlight-twitter::before {
  animation: grow-left-95 200ms 250ms ease-out;
  background-color: #49CEF9;
  width: 95px;
}

.highlight-text::before {
  animation: grow-left-55 200ms 250ms ease-out;
  background-color: #F94970;
  width: 55px;
}

@keyframes grow-left-55 {
  0% { width: 0; }
  100% { width: 55px; }
}

@keyframes grow-left-95 {
  0% { width: 0; }
  100% { width: 95px; }
}
</style>
