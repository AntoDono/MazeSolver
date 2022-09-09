<template>
    <div class="bg-gray-300 w-12 h-12 tile" @click="clicked" ref="tile">
    </div>
</template>

<script>
    export default{
        props:{
            row: {
                default: null,
                type: Number
            },
            col: {
                default: null,
                type: Number
            }
        },
        data(){
            return {
                activated: false
            }
        },
        methods: {
            clicked(){
                this.activated = !this.activated
                this.$refs["tile"].classList.toggle("activated")
                this.$emit("clicked", { row: this.row, col: this.col, activated: this.activated})
            },
            visit(){
                this.$refs["tile"].classList.remove("activated")
                this.$refs["tile"].classList.add("visited")
            },
            endpoint(mode){
                if (mode == 0) this.$refs["tile"].classList.add("start")
                else if (mode == 1) this.$refs["tile"].classList.add("end")
            }
        }
    }
</script>


<style>
    .tile{
        border: 2px solid black;
        transition: all ease 0.2s;
    }
    .activated{
        background-color: rgb(98, 252, 98);
    }
    .visited{
        background-color: rgb(247, 131, 54) !important;
    }
    .start{
        background-color: rgb(62, 139, 255);
    }
    .end{
        background-color: rgb(225, 48, 25);
    }
</style>