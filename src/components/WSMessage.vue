<template>
<div class="container" v-show="condis" @click="close" @touch="close">
  <transition name="meswin">
    <div class="Meswindow" v-show="windis">
      <div class="title">{{title}}</div>
      <div class="text">{{text}}</div>
      <div class="buttongroup">
        <button name="confirm"  @click="confirm"  v-show="type!=='noconfirm'">确定</button>
        <button name="cancel" @click="cancel" >{{ifduring}}再玩一局</button>
      </div>
    </div>
  </transition>
</div>
</template>

<script>
    import bus from '../assets/EventBus'
    export default {
        name: 'WSMessage',
        data() {
            return {
              windis:false,
              condis:false,
              type:'default',
              title:'',
              text:'',
              during:undefined,
              timer:undefined,
              callback:undefined,
              callback2:undefined,
              handle:undefined
            }
        },
        methods: {
          confirm(){
            this.handle=window.clearTimeout(this.handle);
            this.timer=window.clearInterval(this.timer);
            if(this.callback){
              this.callback();
            }

            this.close();
          },
          cancel(){
            // bus.$emit('info','已关闭');
            this.handle=window.clearTimeout(this.handle);
            this.timer=window.clearInterval(this.timer);
            if(this.callback2){
              this.callback2();
            }
            this.close();
          },
          close(){
            this.windis=false;
            window.setTimeout(()=>{
              this.condis=false;
            },500)
          }
        },
        created() {

        },
        mounted() {

          document.addEventListener('touchend',e=>{
            if(e.path[0].name) {
              switch (e.path[0].name) {
                case 'cancel':
                  this.cancel();
                  break;
                case 'confirm':
                  this.confirm();
                  break;
              }
            }

          },false)
          bus.$on('message',(title,text,type='default',during=undefined,callback=undefined,callback2=undefined)=>{
            // alert(1)
            this.title=title;
            this.text=text;
            this.type=type;
            this.during=during;
            this.condis=true;
            this.timer=undefined;
            this.handle=undefined;
            this.callback=callback;
            this.callback2=callback2;
            window.setTimeout(()=>{
              this.windis=true;
            },100);
            if(during&&during>0){
              this.handle=window.setTimeout(()=>{
                this.cancel();
              },during);
              this.timer=window.setInterval(()=>{
                this.during-=1000;
              },1000)
            }
          })
        },
        computed:{
          ifduring(){
            if(this.during&&this.during<0){
              window.clearInterval(this.timer);
            }
            return this.during&&this.during>0?`(${this.during/1000})`:'';
          }
        }
    }
</script>

<style scoped>
  .container{
    position: fixed;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.62);
    cursor:default;
    z-index:950;
  }
  .Meswindow{
    position:fixed;
    width: 400px;
    height: 300px;
    left: calc(50% - 200px);
    top: calc(50% - 150px);
    background: #fff;
    border-radius: 5px;
  }
  .title{
    width:90%;
    margin-left:5%;
    height:35px;
    font-size:20px;
    font-weight: 100;
    font-family: "微软雅黑";
    text-align: center;
    color: #47d1ff;
    line-height: 35px;
    border-bottom:1px solid #e4e4e4;
  }
  .text{
    width:80%;
    margin-left:10%;
    margin-right:10%;
    margin-top:50px;
    font-family: '微软雅黑';
    font-weight:600;
    font-size:18px;
    text-align: center;
  }
  .buttongroup{
    position: absolute;
    bottom:5px;
    width:100%;
    text-align: center;
  }
  .buttongroup>button{
    width:90px;
    height: 35px;
    background: #fff;
    border:1px solid #e4e4e4;
    border-radius: 5px;
    cursor:pointer;
    margin:10px 10px 0 0;
  }
  .buttongroup>button:hover{
    border-color: aqua;
  }
  .buttongroup>button:first-child{
    background: #ceffdc;
  }
  .meswin-enter-active{
    transform-origin:50% top;
    transition:all .5s ease-out;
  }
  .meswin-leave-active{
    transition: all .5s ease;
  }
  .meswin-enter{
    transform:rotateX(75deg) matrix(1,1,1.5,1,1,1.5);
    opacity:0;
    /*transform: translate3d(0,-15,5px);*/
  }
  .meswin-leave-to{
    opacity:0;
  }
</style>
