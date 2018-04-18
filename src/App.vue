<template>
  <div id="app">
    <div class="monitor">
      <Monitor :blockWidth="blockWidth"
               :blockHeight="blockHeight"
               :blockSpace="blockSpace"
               :fontSize="fontSize"
      ></Monitor>
    </div>

  </div>
</template>

<script>
import Monitor from './components/Ch-monitor'
export default {
  name: 'App',
  components: {
    Monitor
  },
  data(){
    return{
      blockWidth:95,
      blockHeight:95,
      blockSpace:15,
      fontSize:40
    }
  },
  methods:{
    init(){
      let clientWidth = document.documentElement.clientWidth;
      if(clientWidth>530){
        this.blockWidth=95;
        this.blockHeight=95;
        this.blockSpace=15;
        this.fontSize=40;
      }else{
        this.blockWidth=clientWidth*0.19;
        this.blockHeight=clientWidth*0.19;
        this.blockSpace=clientWidth*0.03;
        this.fontSize=clientWidth*0.09;
      }
    }
  },
  mounted(){
    window.onresize=()=>{
      this.init();
    };
    window.onload=()=>{
      this.init();
    }
  }
}
</script>

<style>
  .monitor{
    position:absolute;
    left: 50%;
    top: 50%;
  }
  @media (max-width: 530px){
    .monitor{
      left:5%;
      top: 20%;
      width:90%;
      padding-bottom:90%;
    }
  }
  @media (min-width: 530px){
    .monitor{
      margin:0;
      width:455px;
      height:455px;
      transform: translate(-50%,-50%);
    }
  }
</style>
