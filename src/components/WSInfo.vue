<template>
<div class="container">
  <transition-group name="slide-fade" tag="div">
    <div class="info-cover" v-for="item in infolist" v-show="item.dis" :key="item.id">
      <div class="type"><span>i</span></div>
      <span class="text">{{item.text}}</span>
    </div>
  </transition-group>
</div>
</template>

<script>
    import bus from '../assets/EventBus'
    export default {
        name: 'WSInfo',
        data() {
            return {
              id:1,
              infolist:[
              ],

            }
        },
        methods: {
          checkout(){
            for(let item in this.infolist){
              let temp=this.infolist[item];
              if(temp.dis&&!temp.fin){
                temp.fin=true;
                window.setTimeout(()=>{
                  temp.dis=false;
                },temp.during);
              }
            }
          }
        },
        created() {

        },
        mounted() {
          this.checkout();
          bus.$on('info',(text,type='default',during=2000)=>{
            this.infolist.unshift({
              id:++this.id,
              type:type,
              text:text,
              dis:true,
              fin:false,
              during:during
            });
            this.checkout();
          })
        }
    }
</script>

<style scoped>
  .container{
    position:fixed;
    z-index:1000;
    width:380px;
    top:20px;
    left:50%;
    transform: translateX(-50%);
    font-family: '微软雅黑';
  }
  .info-cover{
    width: 100%;
    height: 60px;
    border:1px solid #ebeef5;
    background: #edf2fc;
    border-radius: 5px;
    margin-bottom:5px;
    /*box-shadow: inset 0 0 5px #dddddd;*/
  }
  .type{
    width: 25px;
    height: 25px;
    border-radius: 50%;
    color:#edf2fc;
    font-size: 20px;
    font-weight:800;
    background: #b1b1b1;
    margin-top:16px;
    margin-left:10px;
  }
  .type>span{
    margin-left:10px;
    font-family: "serif";
  }
  .text{
    /*position:absolute;*/
    /*left:60px;*/
    /*top:20px;*/
    position: relative;
    top:-24px;
    margin-left:50px;
    font-size: 15px;
    font-weight:400;
    color: #909399;
  }
  .slide-fade-enter-active {
    transition: all .3s ease-in;
  }
  .slide-fade-leave-active {
    transition: all .8s ease-out;
  }
  .slide-fade-enter, .slide-fade-leave-to
    /* .slide-fade-leave-active for below version 2.1.8 */ {
    transform: translateY(-10px);
    opacity: 0;
  }
</style>
