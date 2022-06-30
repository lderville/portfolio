<template>
  <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
  
  <helloworld></helloworld>
	<earth></earth>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import earth from './components/earth.vue'


export default {
  name: 'App',
  components: {
    'earth' : earth,
    'helloworld' : HelloWorld
  },

}
</script>

<style>
body{
  background-color: aqua;
}
</style>
