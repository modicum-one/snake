<template>
  <div class="container">
    length:{{snake.length}}
    direction:{{direction.x}},{{direction.y}}
    dead:{{dead}}
    speed:{{speed}}}
    <button @click="startGame">Start</button>
    <table style="margin: auto">
      <tr v-for="y in height" v-bind:key="'y_'+y">
        <td v-for="x in width" v-bind:key="'x_'+x"
        >
          <div class="cell" :class="'cell_'+board[y-1][x-1]">
          </div>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
let gameLoop=null;
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data: function() {
    return {
      width:10,
      height:10,
      dead:false,
      direction: {x:0,y:-1},
      board:[],
      snake:[],
      speed:200
    }
  },
  methods:{
    handleInput(event){
      let key = event.key;
      let newDirection = null;
      if("ArrowLeft"===key) {
        newDirection = {x: -1, y: 0}
        event.preventDefault()
      }else if("ArrowRight"===key) {
        newDirection = {x: 1, y: 0}
        event.preventDefault()
      }else if("ArrowUp"===key) {
        newDirection = {x: 0, y: -1}
        event.preventDefault()
      }else if("ArrowDown"===key) {
        newDirection = {x: 0, y: 1}
        event.preventDefault()
      }
      if(newDirection) {
        let x = this.snake[0].x + newDirection.x;
        let y = this.snake[0].y + newDirection.y;
        //Be nice and only let players turn to free blocks
        if (x >= 0 && x < this.width && y >= 0 && y < this.height && this.board[y][x] != 1) {
          this.direction = newDirection;
        }
      }
    },
    addInputHandler(){
      const self = this;
      document.addEventListener('keydown', function(event) {
        self.handleInput(event);
      });
    },
    putRandomEat(){
      // eslint-disable-next-line no-constant-condition
      while(true) {
        let y = Math.floor((Math.random() * 10));
        let x = Math.floor((Math.random() * 10));
        if (this.board[y][x] == 0) {
          this.board[y][x] = 2;
          return;
        }
      }
    },
    andDead(){
      this.dead=true;
      clearInterval(gameLoop)
      gameLoop=null;
      return true;
    },
    startGame(){
      if(!gameLoop){
        clearInterval(gameLoop)
      }
      this.board =Array(this.height).fill(null).map(()=>Array(this.width).fill(0))
      let location = {x:Math.floor(this.height/2),y:Math.floor(this.width/2)}
      this.board[location.y][location.x]=1
      this.snake=[];
      this.snake.push(location);
      this.putRandomEat();
      this.addInputHandler();
      gameLoop = setInterval(()=>{
        let x = this.snake[0].x+this.direction.x;
        let y = this.snake[0].y+this.direction.y;
        if(x<0||x>=this.width||y<0||y>=this.height){
          return this.andDead();
        }
        this.snake.unshift({x:x, y:y })
        if(this.board[y][x]===1){
          return this.andDead();
        }else if(this.board[y][x]===0){//Shrink the end of the snake
          let tail = this.snake.pop();
          this.board[tail.y][tail.x]=0;
        }else{
          this.putRandomEat();
        }
        this.board[y][x]=1; //Add a snake block to the front
      }, this.speed)
    }
  },
  created() {
    // this.board = new Array(this.height).fill(0).map(() => new Array(this.width).fill(0));
    console.log('created!')
    this.startGame()
  }
}
</script>
<style>
table{
  /*border: 1pt black solid;*/
  margin: auto;
  padding: 0;
}
td{
  /*border: 1pt black solid;*/

}
td>div{
  height: 30px;
  width: 30px;
  margin: 0;
  padding: 0;
  background-color: aqua;
}
.container{
  margin: auto;
}
.cell_0{
  background-color: aqua;
}
.cell_1{
  background-color: blue;
}
.cell_2{
  background-color: green;
}
</style>