<template>
  <div class="container">
    <div v-for="(item,i) of chessMap">
      <Block v-for="(block,j) of item"
             number=0
             :width="blockWidthOp"
             :height="blockHeightOp"
             :top="i*(blockHeightOp+blockSpaceOp)+blockSpaceOp"
             :left="j*(blockWidthOp+blockSpaceOp)+blockSpaceOp"
             :key="j"
             display="true"
      ></Block>
    </div>
    <transition-group name="zoom_fade">
      <Block v-for="(item,i) of blockList"
             :number="item.number"
             :width="blockWidthOp"
             :height="blockHeightOp"
             :top="item.i*(blockHeightOp+blockSpaceOp)+blockSpaceOp"
             :left="item.j*(blockWidthOp+blockSpaceOp)+blockSpaceOp"
             :key="item.id"
             :display="item.display"
             :fontSize="fontSizeOp"
             v-if="item.display"
      ></Block>
    </transition-group>
  </div>
</template>

<script>
  import Block from './Ch-block';

  export default {
    name: 'Ch-monitor',
    props: ["blockWidth","blockHeight","blockSpace","fontSize"],
    components: {
      Block
    },
    data() {
      return {
        chessMap: [],
        blockList: [],
        mapping: [],
        defaults:{
          blockWidth: 95,
          blockHeight: 95,
          blockSpace: 15,
          fontSize:40
        },
        total: 0,
        backup:[]
      }
    },
    methods: {
      init() {   //初始化4x4的“棋盘”
        for (let i = 0; i < 4; i++) {
          this.chessMap[i] = [0, 0, 0, 0];
          this.mapping[i] = [null, null, null, null];
        }
        //随机摆放两个点
        for (let i = 0; i < 2; i++) {
          this.newBlock();
        }
        this.refreshMap();
      },
      refreshMap() {
        for (let i = 0; i < 4; i++) {
          for (let j = 0; j < 4; j++) {
            this.chessMap[i][j] = 0;
            this.mapping[i][j] = null;
          }
        }
        let i = 0;
        for (const blockListElement of this.blockList) {
          if (blockListElement.display) {
            this.chessMap[blockListElement.i][blockListElement.j] = blockListElement.number;
            this.mapping[blockListElement.i][blockListElement.j] = i;  //建立映射关系
          }
          i++;
        }
      },
      checkMap(i, j) {
        return (this.chessMap[i][j] === 0);
      },
      randomPos() {
        let i, j;
        while (true) {
          i = Math.floor(Math.random() * 4);
          j = Math.floor(Math.random() * 4);
          if (this.chessMap[j][i] === 0) {
            return {i, j};
          }
        }
      },
      newBlock() {
        const tempPos = this.randomPos();
        this.blockList.push({i: tempPos.j, j: tempPos.i, number: 2, id: this.total++, display: true});
        this.refreshMap();
      },
      ifDead() {
        let flag = true;
        for (let i = 0; i < 4; i++) {
          for (let j = 0; j < 4; j++) {
            if (this.chessMap[i][j] === 0) {
              flag = false;
            }
          }
        }
        if (flag) {
          //棋盘无空且操作无效，检测是否还可以继续游戏
          const tempMap = JSON.parse(JSON.stringify(this.chessMap));
          const tempList = JSON.parse(JSON.stringify(this.blockList));
          if (!this.upOp) {
            this.chessMap = JSON.parse(JSON.stringify(tempMap));
            this.tempList = JSON.parse(JSON.stringify(tempList));
            if (!this.downOp) {
              this.chessMap = JSON.parse(JSON.stringify(tempMap));
              this.tempList = JSON.parse(JSON.stringify(tempList));
              if (!this.leftOp) {
                this.chessMap = JSON.parse(JSON.stringify(tempMap));
                this.tempList = JSON.parse(JSON.stringify(tempList));
                if (!this.rightOp) {
                  this.chessMap = JSON.parse(JSON.stringify(tempMap));
                  this.tempList = JSON.parse(JSON.stringify(tempList));
                } else {
                  return true;
                }
              } else {
                return true;
              }
            } else {
              return true;
            }
          } else {
            return true;
          }
        }
      },
      upOp() {
        let flag = false;
        //当按下上箭头时遍历blockList，处理每个可以向上推动的单元
        for (let j = 0; j < 4; j++) {
          for (let i = 0; i < 4; i++) {

            if (!this.blockList[this.mapping[i][j]] || !this.blockList[this.mapping[i][j]].display) continue;
            let blockListElement = this.blockList[this.mapping[i][j]];

            while (blockListElement.i > 0 &&
            blockListElement.display && (this.chessMap[blockListElement.i - 1][blockListElement.j] === blockListElement.number ||
              this.chessMap[blockListElement.i - 1][blockListElement.j] === 0
            )) {
              if (this.chessMap[blockListElement.i - 1][blockListElement.j] === 0) {
                //如果上方为空则直接挪动

              } else {
                //否则删除上方被合并节点，翻倍并挪动下方节点
                this.blockList[this.mapping[blockListElement.i - 1][blockListElement.j]].display = false;
                blockListElement.number *= 2;

              }
              flag = true;
              blockListElement.i--;
              //最后更新一下棋盘
              this.refreshMap();
            }
          }
        }
        return flag;
      },
      downOp() {
        let flag = false;
        //当按下下箭头时遍历blockList，处理每个可以向下推动的单元，此处应注意遍历从下向上进行
        for (let j = 0; j < 4; j++) {
          for (let i = 3; i >= 0; i--) {
            if (!this.blockList[this.mapping[i][j]] || !this.blockList[this.mapping[i][j]].display) continue;
            let blockListElement = this.blockList[this.mapping[i][j]];

            while (blockListElement.i < 3 &&
            blockListElement.display &&
            (this.chessMap[blockListElement.i + 1][blockListElement.j] === blockListElement.number ||
              this.chessMap[blockListElement.i + 1][blockListElement.j] === 0
            )) {
              if (this.chessMap[blockListElement.i + 1][blockListElement.j] === 0) {
                //如果下方为空则直接挪动
                flag = true;
              } else {
                //否则删除下方被合并节点，翻倍并挪动上方节点
                let i = 0;
                this.blockList[this.mapping[blockListElement.i + 1][blockListElement.j]].display = false;

                blockListElement.number *= 2;

                flag = true;
              }

              blockListElement.i++;
              //最后更新一下棋盘
              this.refreshMap();
            }
          }
        }
        return flag;
      },
      leftOp() {
        let flag = false;
        //当按下←箭头时遍历blockList，处理每个可以向左推动的单元
        for (let i = 0; i < 4; i++) {
          for (let j = 0; j < 4; j++) {
            if (!this.blockList[this.mapping[i][j]] || !this.blockList[this.mapping[i][j]].display) continue;
            let blockListElement = this.blockList[this.mapping[i][j]];
            while (blockListElement.j > 0 &&
            blockListElement.display &&
            (this.chessMap[blockListElement.i][blockListElement.j - 1] === blockListElement.number ||
              this.chessMap[blockListElement.i][blockListElement.j - 1] === 0
            )) {
              if (this.chessMap[blockListElement.i][blockListElement.j - 1] === 0) {
                //如果左方为空则直接挪动
                flag = true;
              } else {
                //否则删除左方被合并节点，翻倍并挪动右方节点
                this.blockList[this.mapping[blockListElement.i][blockListElement.j - 1]].display = false;
                blockListElement.number *= 2;

                flag = true;
              }
              blockListElement.j--;
              //最后更新一下棋盘
              this.refreshMap();
            }
          }
        }
        return flag;
      },
      rightOp() {
        let flag = false;
        //当按下右箭头时遍历blockList，处理每个可以向右推动的单元，此处同样需要注意遍历顺序从右向左
        for (let i = 0; i < 4; i++) {
          for (let j = 3; j >= 0; j--) {
            if (!this.blockList[this.mapping[i][j]] || !this.blockList[this.mapping[i][j]].display) continue;
            let blockListElement = this.blockList[this.mapping[i][j]];
            while (blockListElement.j < 3 &&
            blockListElement.display &&
            (this.chessMap[blockListElement.i][blockListElement.j + 1] === blockListElement.number ||
              this.chessMap[blockListElement.i][blockListElement.j + 1] === 0
            )) {
              if (this.chessMap[blockListElement.i][blockListElement.j + 1] === 0) {
                //如果右方为空则直接挪动

              } else {
                //否则删除右方被合并节点，翻倍并挪动左方节点
                this.blockList[this.mapping[blockListElement.i][blockListElement.j + 1]].display = false;
                blockListElement.number *= 2;

              }
              flag = true;
              blockListElement.j++;
              //最后更新一下棋盘
              this.refreshMap();
            }
          }
        }
        return flag;
      },
      doBackup(){
        this.backup.push({
          chessMap:JSON.stringify(this.chessMap),
          blockList:JSON.stringify(this.blockList),
          mapping:JSON.stringify(this.mapping),
          total:JSON.stringify(this.total)
        });
      }
    },
    created() {
      this.init();
    },
    mounted() {
      document.onkeydown = e => {
        let flag = false;
        switch (e.key) {
          case 'Backspace':
            if(this.backup.length>0){
              const backup=this.backup.pop();
              this.chessMap=JSON.parse(backup.chessMap);
              this.blockList=JSON.parse(backup.blockList);
              this.mapping=JSON.parse(backup.mapping);
              this.total=JSON.parse(backup.total);
            }
            break;
          case 'ArrowUp':
            this.doBackup();
            flag = this.upOp();
            break;
          case 'ArrowDown':
            this.doBackup();
            flag = this.downOp();
            break;
          case 'ArrowLeft':
            this.doBackup();
            flag = this.leftOp();
            break;
          case 'ArrowRight':
            this.doBackup();
            flag = this.rightOp();
            break;
        }

        if (flag) {
          this.newBlock();

        } else {
          if (this.ifDead()) {
            //游戏结束

          }
        }
      };
      document.ontouchstart=e=>{
        if (event.cancelable) {
          // 判断默认行为是否已经被禁用
          if (!event.defaultPrevented) {
            event.preventDefault();
          }
        }
      };
      document.ontouchmove=e=>{
        if (event.cancelable) {
          // 判断默认行为是否已经被禁用
          if (!event.defaultPrevented) {
            event.preventDefault();
          }
        }
      };
      document.ontouchend=e=>{
        if (event.cancelable) {
          // 判断默认行为是否已经被禁用
          if (!event.defaultPrevented) {
            event.preventDefault();
          }
        }
      }
    },
    computed: {
      blockWidthOp(){
        return this.blockWidth||this.defaults.blockWidth;
      },
      blockHeightOp(){
        return this.blockHeight||this.defaults.blockHeight;
      },
      blockSpaceOp(){
        return this.blockSpace||this.defaults.blockSpace;
      },
      fontSizeOp(){
        return this.fontSize||this.defaults.fontSize;
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .container {
    width: 100%;
    /*height: 100%;*/
    padding-bottom: 100%;
    border-radius: 10px;
    background: #cfc0b4;
  }

  .zoom_fade-enter, .zoom_fade-leave-to {
    opacity: 0;
    transform: scale(0.1, 0.1);
    transform-origin: center;
  }

  .zoom_fade-enter-active, .zoom_fade-leave-active {
    transition: all ease 0.5s;
  }
</style>
