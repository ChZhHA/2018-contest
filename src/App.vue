<template>
  <div id="app">
    <div class="hint">回溯：电脑Backspace 或 双指点击</div>
    <div class="scoll" :style="'font-size:'+(fontSize*0.75)+'px;'">分数{{scoll}}</div>
    <div class="monitor">
      <Monitor :blockWidth="blockWidth"
               :blockHeight="blockHeight"
               :blockSpace="blockSpace"
               :fontSize="fontSize"
      ></Monitor>
    </div>
    <div class="hint2">
      <p>目标是凑出2048点！前进!</p>
      <p><strong>提示</strong>:电脑使用方向键操作 <br> 手机滑动屏幕操作</p>
    </div>
    <!--信息窗口-->
    <Info></Info>

    <!--消息窗口-->
    <Message></Message>
  </div>
</template>

<script>
import Monitor from '@/components/CHMonitor'
import Info from '@/components/WSInfo'
import Message from '@/components/WSMessage'
import bus from './assets/EventBus'
export default {
  name: 'App',
  components: {
    Monitor,
    Info,
    Message
  },
  data(){
    return{
      scoll:0,
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
        this.fontSize=38;
      }else{
        this.blockWidth=clientWidth*0.19;
        this.blockHeight=clientWidth*0.19;
        this.blockSpace=clientWidth*0.03;
        this.fontSize=clientWidth*0.087;
      }
    }
  },
  mounted(){
    window.onresize=()=>{
      this.init();
    };
    window.onload=()=>{
      this.init();
      bus.$emit('info','欢迎来到陈宗豪的2048');
    };
    bus.$on('scoll',(s)=>{
      this.scoll=s;
    })
  }
}
</script>

<style>
  .monitor{
    position:absolute;
    left: 50%;
    top: 50%;
  }
  .scoll{
    position:absolute;
    text-align: center;
    top:50px;
    left:50%;
    transform: translateX(-50%);
    color:black;
  }
  .hint{
    position: absolute;
    text-align: center;
    left:50%;
    top:0;
    color: #f4f4f4;
    transform: translateX(-50%);
  }
  .hint2{
    position:absolute;
    text-align: center;
    color: #666666;
  }
  .hint2>p{
    margin:0;
  }
  @media (max-width: 530px){
    .monitor{
      left:5%;
      top: 20%;
      width:90%;
      /*padding-bottom:90%;*/
    }
    .hint2{
      left:50%;
      bottom:0%;
      transform: translateX(-50%);
    }
  }
  @media (min-width: 530px){
    .monitor{
      margin:0;
      width:455px;
      height:455px;
      transform: translate(-50%,-50%);
    }
    .hint2{
      left:50%;
      bottom:5%;
      transform: translateX(-50%);
    }
  }
</style>
