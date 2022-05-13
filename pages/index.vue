<template>
  <div class="h-screen w-screen flex justify-center items-center flex-col">
    <h1 class="text-5xl">{{message}}</h1>
    <div class="border-2 border-black">
      <div v-for="outer in row_max" :key="outer" class="flex justify-center items-center flex-row">
        <box v-for="inner in col_max" :key="inner" :ref="`${outer-1}-${inner-1}`" :row="outer-1" :col="inner-1" @clicked="selected"/>
      </div>
    </div>
    <button class="relative top-10 text-2xl font-white bg-green-400 pl-4 pr-4 pt-2 pb-2" @click="solve">Solve</button>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data(){
    return {
      matrix: (()=>{
        var m = []
        for (let o = 0; o < 10; o ++){
          m.push([])
          for (let i = 0; i < 10; i ++){
            m[o].push(false)
          }
        }
        return m
      })(),
      start: [],
      end: [],
      message: "Select starting spot",
      action: 0,
      solutions: 0,
      row_max: 10,
      col_max: 10,
      stack_called: 0
    }
  },
  methods:{
    selected(row, col, state){
      if (this.action==0){
        console.log(`Start ${row}, ${col}`)
        this.matrix[row][col] = state
        this.start = [row, col]
        this.action = 1
        this.message = "Select the ending spot"
        this.$refs[`${row}-${col}`][0].set_end("start")
      }else if (this.action==1){
        console.log(`End ${row}, ${col}`)
        this.matrix[row][col] = state
        this.end = [row, col]
        this.action = 2
        this.message = "Select the tiles to create paths"
        this.$refs[`${row}-${col}`][0].set_end("end")
      }else{
        console.log(`Path ${row}, ${col}`)
        this.matrix[row][col] = state
      }
    },
    async solve(){
      await this.breadth_depth(this.start)
      this.message = "Solving..."
    },
    async breadth_depth(coords){

      this.stack_called++
      console.log("Current: " + coords + " Stack called: " + this.stack_called)

      let neighbors = [], row = coords[0], col = coords[1]

      if (
        !(
          (row == this.start[0] && col == this.start[1]) ||
          (row == this.end[0] && col == this.end[1])
        )
      ){
        console.log("settings")
        this.$refs[`${row}-${col}`][0].set_traveled()
      }
      

      if (row == this.end[0] && col == this.end[1]) {
        this.solutions++;
        console.log(this.solutions)
        this.message = `Found ${this.solutions} solutions to this maze.`
        return
      }

      if (row+1 < this.row_max) if (this.matrix[row+1][col] == true) neighbors.push([row+1, col])
      if (row-1 >= 0) if (this.matrix[row-1][col] == true) neighbors.push([row-1, col])
      if (col+1 < this.col_max) if (this.matrix[row][col+1] == true) neighbors.push([row, col+1])
      if (col-1 >= 0) if (this.matrix[row][col-1] == true) neighbors.push([row, col-1])

      this.matrix[row][col] = false

      neighbors.forEach(async(coords)=>{
        await this.sleep(100)
        this.breadth_depth(coords)
      })
    },
    sleep(ms) {
      return new Promise(resolve => setTimeout(resolve, ms));
    }
  },
  mounted(){
    console.log(this.matrix)
  }
}
</script>
