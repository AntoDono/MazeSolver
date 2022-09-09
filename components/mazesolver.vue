<template>
    <div class="flex justify-center flex-col gap-y-5">
        <h2 class="text-center text-xl">{{directions}}</h2>
        <div class="flex justify-center items-center">
            <div v-for="row in rows" :key="row">
                <tile v-for="col in cols" :key="col" :row="row" :col="col" @clicked="clicked" :ref="`r${row}-c${col}`"/>
            </div>
        </div>
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
                directions: "",
                mode: 0 // 0 means select start, 1 means select end, 2 means selecting paths
            }
        },
        methods:{
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
                this.isPath[pos.row][pos.col] = pos.activated

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
            initialize(){

                /*Initializing Lookup*/

                // add the first row 0 to be false, setting columns to be 1 - indexed
                this.lookup.push([])
                for (let col = 0; col <= this.cols; col++){
                    this.lookup[0].push(false)
                }
        
                // filling up the rest of the lookup
                for (let row = 1; row <= this.rows; row++){
                    this.lookup.push([false]) // filling the index 0 col, making the columns 1 - indexed
                    for (let col = 1; col <= this.cols; col++){
                        this.lookup[row].push(false) // filling the rest with 0.
                    }
                }

                /*Initializing isPath*/
                
                // add the first row 0 to be false, setting columns to be 1 - indexed
                this.isPath.push([])
                for (let col = 0; col <= this.cols; col++){
                    this.isPath[0].push(false)
                }
        
                // filling up the rest of the lookup
                for (let row = 1; row <= this.rows; row++){
                    this.isPath.push([false]) // filling the index 0 col, making the columns 1 - indexed
                    for (let col = 1; col <= this.cols; col++){
                        this.isPath[row].push(false) // filling the rest with 0.
                    }
                }

            }
        },
        mounted(){

            this.initialize()
            this.directions = "Please select the starting point"
            console.log(this.$refs)
        }
    }
</script>