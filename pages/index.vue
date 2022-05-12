<template>
  <div class="h-screen w-screen flex justify-center items-center flex-col">
    <h1 class="text-5xl">{{message}}</h1>
    <div class="border-2 border-black">
      <div v-for="outer in 10" :key="outer" class="flex justify-center items-center flex-row">
        <box v-for="inner in 10" :key="inner" :ref="`${outer-1}-${inner-1}`" :row="outer-1" :col="inner-1" @clicked="selected"/>
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
      solutions: 0
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
        this.start = [row, col]
        this.action = 2
        this.message = "Select the tiles to create paths"
        this.$refs[`${row}-${col}`][0].set_end("end")
      }else{
        console.log(`Path ${row}, ${col}`)
        this.matrix[row][col] = state
      }
    },
    solve(){
      this.breadth_depth(this.start)
      this.message = `Found ${solutions} solutions to this maze.`
    },
    breadth_depth(coords){

      if (coords == this.end) {
        this.solutions++;
        return
      }

      let neighbor = [], row = coords[0], col = coords[1]

      if (this.matrix[row+1][col]!=undefined) neighbor.push([row+1, col])
      if (this.matrix[row-1][col]!=undefined) neighbor.push([row-1, col])
      if (this.matrix[row][col-1]!=undefined) neighbor.push([row, col-1])
      if (this.matrix[row][col+1]!=undefined) neighbor.push([row, col+1])

      neighbor.forEach((coords)=>{this.breadth_depth(coords)})

    }
  },
  mounted(){
    console.log(this.matrix)
  }
}
</script>
