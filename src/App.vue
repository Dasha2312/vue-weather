
<template>
  <main>
    <WeatherApp />
  </main>
</template>

<script>

import WeatherApp from './components/WeatherApp.vue';

export default {
	name: 'weather-wrapp',
    components: {
		WeatherApp
  	},
}
</script>

<style>
@import './assets/base.css';

#app {
  font-weight: normal;
  height: 100%;
}
main {
  height: 100%;
}

</style>
