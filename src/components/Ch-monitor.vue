<template>
  <div class="container">
    <div v-for="(item,i) of chessMap">
      <Block v-for="(block,j) of item"
             number=0
             :width="blockWidth"
             :height="blockHeight"
             :top="i*(blockHeight+blockSpace)+blockSpace"
             :left="j*(blockWidth+blockSpace)+blockSpace"
             :key="block"
      ></Block>
    </div>
    <Block v-for="item of blockList"
           :number="item.number"
           :width="blockWidth"
           :height="blockHeight"
           :top="item.y*(blockHeight+blockSpace)+blockSpace"
           :left="item.x*(blockWidth+blockSpace)+blockSpace"
           :key="item.id"
    ></Block>
  </div>
</template>

<script>
  import Block from './Ch-block';
    export default {
        name: 'Ch-monitor',
        props: [],
        components: {
          Block
        },
        data() {
            return {
              chessMap:[],
              blockList:[],
              blockWidth:95,
              blockHeight:95,
              blockSpace:15
            }
        },
        methods: {
          init(){   //初始化4x4的“棋盘”
            for (let i = 0; i < 4; i++) {
              this.chessMap[i]=[];
            }

            //随机摆放两个点
            const tempX=Math.floor(Math.random()*4);
            const tempY=Math.floor(Math.random()*4);
            this.blockList.push({x:tempX,y:tempY,number:2,id:1});
            let tempX2=tempX,tempY2=tempY;
            while(tempX2===tempX&&tempY2===tempY){
              tempX2=Math.floor(Math.random()*4);
              tempY2=Math.floor(Math.random()*4);
            }
            this.blockList.push({x:tempX2,y:tempY2,number:2,id:2});
            this.checkMap();
          },
          checkMap(){
            for (let i = 0; i < 4; i++) {
              for (let j = 0; j < 4; j++) {
                this.chessMap[i][j]=0;
              }
            }
            for (const blockListElement of this.blockList) {
              this.chessMap[blockListElement.x][blockListElement.y]=blockListElement.number;
            }
          }
        },
        created() {
          this.init();

        },
        mounted() {

        },
        computed: {}
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container{
    width:100%;
    height: 100%;
    border-radius: 10px;
    background: #cfc0b4;
  }
</style>
