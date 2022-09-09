<template>
    <div class="flex justify-center flex-col gap-y-5">
        <h2 class="text-center text-xl">{{directions}}</h2>
        <h2 class="text-center text-xl">Solved: {{solved}}</h2>
        <div class="flex justify-center items-center flex-col">
            <div v-for="row in rows" :key="row" class="flex flex-row">
                <tile v-for="col in cols" :key="col" :row="row" :col="col" @clicked="clicked" :ref="`r${row}-c${col}`"/>
            </div>
        </div>
        <button @click="solve">SOLVE</button>
        <button @click="reset">RESET</button>
    </div>
</template>

<script>

    export default{
        data(){
            return{
                rows: 10,
                cols: 10,
                lookup: [], // 2D array
                isPath: [], // 2D array
                start: {
                    row: null,
                    col: null
                },
                end: {
                    row: null,
                    col: null 
                },
                solved: false,
                directions: "Initializing Map",
                mode: 0, // 0 means select start, 1 means select end, 2 means selecting paths,
                debug: true,
                delay: 50,
            }
        },
        methods:{
            sleep(milliseconds){
                return new Promise(resolve => setTimeout(resolve, milliseconds))
            },
            printLookup(){
                for (let row = 0; row <= this.rows; row++){
                    console.log(`Row ${row}: ${this.lookup[row].toString()}`)
                }
            },
            printPath(){
                for (let row = 0; row <= this.rows; row++){
                    console.log(`Row ${row}: ${this.isPath[row].toString()}`)
                }
            },
            clicked(pos){
                if (pos.activated) this.isPath[pos.row][pos.col] = 1
                else this.isPath[pos.row][pos.col] = 0

                if (this.debug) this.printPath(this.isPath[pos.row][pos.col])

                if (this.mode == 0) {
                    this.start.row = pos.row
                    this.start.col = pos.col
                    
                    this.directions = "Please select the ending point" // changes the mode to endpoint
                    this.$refs[`r${pos.row}-c${pos.col}`][0].endpoint(this.mode)
                    this.mode = 1
                    
                }else if (this.mode == 1){
                    this.end.row = pos.row
                    this.end.col = pos.col

                    this.directions = "Please select to create paths" // changes the mode to create paths
                    this.$refs[`r${pos.row}-c${pos.col}`][0].endpoint(this.mode)
                    this.mode = 2
                }
            },
            async dfs(row, col){
                if (this.solved) return
                await this.sleep(this.delay)
                if ( row < 1 || row > this.rows || col < 1 || col > this.cols ) return // check if it is out of bound
                if (this.lookup[row][col] == 1 || this.isPath[row][col] == 0) return // if visited or is not a path return
                if (this.debug) console.log(`Visited r${row} c${col}`)
                this.lookup[row][col] = 1

                this.$refs[`r${row}-c${col}`][0].visit() // makes the tile turn to visited color

                if (row == this.end.row && col == this.end.col) {
                    this.directions = "Reached the End!"
                    this.solved = true
                    return
                }

                await this.dfs(row - 1, col) // visit up tile
                await this.dfs(row + 1, col) // visit down tile
                await this.dfs(row, col - 1) // visit left tile
                await this.dfs(row, col + 1) // visit right tile

                return new Promise( resolve => resolve("finished dfs node"))
            },
            async solve(){
                await this.dfs(this.start.row, this.start.col)
            },
            reset(){
                for (let row = 1; row <= this.rows; row++){
                    for (let col = 1; col <= this.cols; col++){
                        this.lookup[row][col] = 0
                        this.isPath[row][col] = 0
                        this.$refs[`r${row}-c${col}`][0].reset()
                    }
                }

                this.solved = false
                this.mode = 0
                
                this.initialize()
                this.directions = "Please select the starting point"
            },
            initialize(){

                /*Initializing Lookup*/

                // add the first row 0 to be false, setting columns to be 1 - indexed
                this.lookup.push([])
                for (let col = 0; col <= this.cols; col++){
                    this.lookup[0].push(-1)
                }
        
                // filling up the rest of the lookup
                for (let row = 1; row <= this.rows; row++){
                    this.lookup.push([-1]) // filling the index 0 col with -1, making the columns 1 - indexed
                    for (let col = 1; col <= this.cols; col++){
                        this.lookup[row].push(0) // filling the rest with 0.
                    }
                }

                /*Initializing isPath*/
                
                // add the first row 0 to be -1, setting columns to be 1 - indexed
                this.isPath.push([])
                for (let col = 0; col <= this.cols; col++){
                    this.isPath[0].push(-1)
                }
        
                // filling up the rest of the lookup
                for (let row = 1; row <= this.rows; row++){
                    this.isPath.push([-1]) // filling the index 0 col with -1, making the columns 1 - indexed
                    for (let col = 1; col <= this.cols; col++){
                        this.isPath[row].push(0) // filling the rest with 0.
                    }
                }

            }
        },
        async mounted(){
            this.initialize()
            this.directions = "Please select the starting point"
        }
    }
</script>