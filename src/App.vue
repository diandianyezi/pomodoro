<template>
  <router-view />
</template>

<style lang="less">
html, body {
  padding: 0;
  margin: 0;
}
body {
  height: 100vh;
  background: radial-gradient(
      circle at 60% 90%,
      rgb(40, 159, 127),
      transparent 60%
    ),
    radial-gradient(
      circle at 20px 20px,
      rgba(40, 159, 127, 0.8),
      transparent 25%
    ),
    #158270;
}
</style>
