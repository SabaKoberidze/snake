<script setup lang="ts">
import {Ref, onMounted, ref } from "vue"
const boardSize = 400
const snakePosition:Ref<number> = ref(2)
const treatPosition: Ref<number> = ref(0)
const snakeLength:Ref<number> = ref(2)
const snakeBody: Ref<number[]> = ref([0,1])
const directions = {
    up: -Math.sqrt(boardSize),
    right: 1,
    left: -1,
    down:  Math.sqrt(boardSize),
}
let previousDirection = directions.right
let currentDirection = directions.right 

let randomNumber = Math.floor(Math.random() * (boardSize - 4)) + 5;
treatPosition.value = randomNumber

setInterval(()=>{
    if(previousDirection === directions.left && currentDirection === directions.right || 
    previousDirection === directions.right && currentDirection === directions.left ||
    previousDirection === directions.up && currentDirection === directions.down ||
    previousDirection === directions.down && currentDirection === directions.up 
    ){
        currentDirection = previousDirection
    }
    snakeBody.value.push(snakePosition.value)
    if(snakeLength.value < snakeBody.value.length){        
        snakeBody.value.splice(0, 1)
    }
    if((snakePosition.value + currentDirection)%Math.sqrt(boardSize) === 0 && currentDirection === directions.right){
        snakePosition.value -= Math.sqrt(boardSize)
    }else if(snakePosition.value%Math.sqrt(boardSize) === 0 && currentDirection === directions.left){
        snakePosition.value += Math.sqrt(boardSize)
    }else if(snakePosition.value + currentDirection < 0 &&  currentDirection === directions.up){
        snakePosition.value += boardSize
    }else if(snakePosition.value + currentDirection > boardSize && currentDirection === directions.down){
        snakePosition.value += -boardSize
    }
    snakePosition.value += currentDirection
    if(snakeBody.value.includes(snakePosition.value)){
        snakeBody.value = [0,1]
        snakeLength.value = 2
        snakePosition.value = 2
        let randomNumber = Math.floor(Math.random() * (boardSize - 4)) + 5;
        treatPosition.value = randomNumber
        currentDirection = directions.right
        previousDirection = directions.right
    }
    if(snakePosition.value === treatPosition.value){
        snakeLength.value++
        let randomNumber = treatPositionGenerator()
        treatPosition.value = randomNumber      
    }
    previousDirection = currentDirection
}, 200)
function treatPositionGenerator(): number{
    do {
    randomNumber = Math.floor(Math.random() * (boardSize+1));
    }while(validateTreat(randomNumber))
    return randomNumber
}
function validateTreat(randomNumber: number): boolean{
    console.log(snakeBody.value.includes(randomNumber), !snakeBody.value.includes(randomNumber))
    snakeBody.value.includes(randomNumber)
    if(randomNumber !== snakePosition.value && !snakeBody.value.includes(randomNumber)){
        return false
    }else{
        return true
    }
}
onMounted(() => {
    window.addEventListener("keydown", e => {
		switch (e.keyCode) {
            case 37:
                currentDirection = directions.left
            break;
            case 38:
                currentDirection = directions.up
            break;
            case 39:
                currentDirection = directions.right
            break;
            case 40:
                currentDirection = directions.down
            break;
            default:
                currentDirection = currentDirection
         }
	});
})


</script>

<template>
 <div class="snakeBoardContainer">
    <div v-for="(, index) in boardSize" class="snakeSlot" v-bind:class="{head: snakePosition === index, body: snakeBody.includes(index), treat: treatPosition === index}"></div>
    </div>
</template>

<style scoped lang="scss">
    .snakeBoardContainer{
        display: grid;
        grid-template-columns: repeat(20, 1fr);
        height: 100vh;
        aspect-ratio: 1/1;
        background-color: black;
        gap: 1px;
        .snakeSlot{         
            width: 100%;
            height: 100%;
            &.head{
                background-color: #0A00D2;
             
            }
            &.body{
                background-color:  #0A00D2;
            }
            &.treat{
                background-color: #d7fa3c;
                animation-name: idle;
                animation-duration: 1500ms;
                animation-timing-function: ease-in-out;
                animation-iteration-count: infinite;
                
            }
            @keyframes idle {
                0%{
                    transform: scale(0.8);
                    opacity: 0.7;
                }
                100%{
                    transform: scale(1);
                    opacity: 1;
                }
            }
        }
    }
</style>