<template>
    <div class="block-cover"
         :style="'width:'+widthOp+'px;height:'+heightOp+'px; background:'+bgColorOp+';color:'+fontColorOp+';line-height:'+heightOp+'px;top:'+top+'px;left:'+left+'px;'"
          v-if="display"
    >
      <span v-show="number>0">{{number}}</span>
    </div>
</template>

<script>
    export default {
        name: 'Ch-block',
        props: ["number","width","height","top","left","display"],
        components: {},
        data() {
            return {
              defaults:{
                width:40,
                height:40
              }
            }
        },
        methods: {},
        created() {

        },
        mounted() {

        },
        computed: {
          fontColorOp(){  //根据数字变化返回字体颜色
            if(parseInt(this.number)<8){
              return `#464646`;
            }else if(parseInt(this.number)<1024){
              return `#f3f3f3`;
            }else{
              return `#ffffff`;
            }
          },
          bgColorOp(){  //根据数字变化返回不同背景颜色
            switch (parseInt(this.number)){
              case 0:
                return `#f5eae4`;
              case 2:
                return `#cdc6be`;
              case 4:
                return `#d5d89e`;
              case 8:
                return `#e3c597`;
              case 16:
                return `#eeaa6a`;
              case 32:
                return `#ee834c`;
              case 64:
                return `#f64f36`;
              case 128:
                return `#f62600`;
              case 256:
                return `#eb9e0d`;
              case 512:
                return `#e9bb0f`;
              case 1024:
                return `#e9d508`;
              case 2048:
                return `#eeee00`;
            }
          },
          widthOp(){
            return this.width||this.defaults.width;
          },
          heightOp(){
            return this.height||this.defaults.height;
          }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .block-cover{
    position:absolute;
    border-radius: 5px;
    margin:0;
    text-align: center;
    font-weight:800;
    font-size:40px;
    font-family: "Comic Sans MS","Arial";
    transition: all 0.5s ease;
  }
</style>
