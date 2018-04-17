<template>
  <div id="app">
    <div class="monitor">
      <Monitor></Monitor>
    </div>

  </div>
</template>

<script>
import Monitor from './components/Ch-monitor'
export default {
  name: 'App',
  components: {
    Monitor
  }
}
</script>

<style>
  .monitor{
    position:absolute;
    left: 50%;
    top: 50%;
    width:455px;
    height:455px;
    transform: translate(-50%,-50%);
  }
</style>
