<template>
  <router-view />
</template>

<style lang="scss">
@import './assets/all';
</style>

<script>
export default {
  name: 'App',
  created() {
    console.log(process.env.VUE_APP_API, process.env.VUE_APP_PATH);
  },
};
</script>
