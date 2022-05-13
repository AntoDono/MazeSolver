<template>
    <div class="w-12 h-12 initial" @click="clicked" ref="box">
    </div>
</template>

<script>

export default {
    name: "box",
    data(){
        return {
            on: false,
            end: false
        }
    },
    props:{
        row: {
            required: true,
            type: Number
        },
        col: {
            required: true,
            type: Number
        },
    },
    methods: {
        clicked(){
            if (!this.end){
                this.on = !this.on
                this.$emit("clicked", this.row, this.col, this.on)
                this.$refs.box.classList.toggle('selected')
            }
        },
        set_end(type){
            if (type=="start"){
                this.end = true
                this.$refs.box.classList.toggle("start")
            }else{
                this.$refs.box.classList.toggle("end")
            }
        },
        set_traveled(){
            this.$refs.box.classList.add("traveled")
        }
    }
}

</script>

<style>

.start{
    border: 2 px solid rgb(127, 202, 14) !important;
    background-color: rgb(127, 202, 14) !important;
}

.end{
    border: 2 px solid rgb(14, 174, 202) !important;
    background-color: rgb(14, 174, 202) !important;
}

.initial{
    border: 2 px solid gray;
    background-color: gray;
    transition: all ease 0.1s;
}

.initial:hover{
    border: 2px solid black !important;
}

.selected{
    background-color: white;
    border-color: white;
}

.traveled{
    border: 2 px solid red !important;
    background-color: red !important;
}

</style>