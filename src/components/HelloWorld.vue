<script setup lang="ts">
import Timer from "./Timer.vue";
import { ref } from 'vue';
const easyColors = [
  '#FF5733', // Reddish Orange
  '#FFC300', // Vivid Yellow
  '#DAF7A6', // Light Green
  '#C70039', // Crimson
  '#900C3F'  // Dark Magenta
];

// Medium (8 colors)
const mediumColors = [
  '#FF5733', // Reddish Orange
  '#FFC300', // Vivid Yellow
  '#DAF7A6', // Light Green
  '#C70039', // Crimson
  '#900C3F', // Dark Magenta
  '#581845', // Deep Purple
  '#2ECC71', // Emerald Green
  '#3498DB'  // Bright Blue
];

// Hard (12 colors)
const hardColors = [
  '#FF5733', // Reddish Orange
  '#FFC300', // Vivid Yellow
  '#DAF7A6', // Light Green
  '#C70039', // Crimson
  '#900C3F', // Dark Magenta
  '#581845', // Deep Purple
  '#2ECC71', // Emerald Green
  '#3498DB', // Bright Blue
  '#F1C40F', // Vivid Gold
  '#E67E22', // Carrot Orange
  '#E74C3C', // Bright Red
  '#95A5A6'  // Grayish
];
const colorArray = ref<string[]>([]);
const colorToMatchArray = ref<string[]>([]);
const difficultyButtons = ref<{
  value: "easy" | "medium" | "hard",
  text: string,
}[]>([{
  value: "easy",
  text: "Easy"
},{
  value: "medium",
  text: "Medium"
}, {
  value: "hard",
  text: "Hard"
}]);
const gameStart = ref<boolean>(false);
const gameWin = ref<boolean>(false);
const selectedColorIndex = ref<number | null>(null);
const countMatchRef = ref<number>(0);

function onClickLevel(level: 'easy' | "medium" | "hard"){
  const colorArrayToBe = level == "easy" ? easyColors : level == "medium" ? mediumColors : hardColors;
  colorArray.value = shuffleArray(colorArrayToBe);
  colorToMatchArray.value = shuffleArray(colorArrayToBe);
  gameStart.value = true;
  countMatchColor();
}

function shuffleArray(array: string[]){
  const shuffled = [...array]; // Create a copy of the array to avoid mutating the original
  for (let i = shuffled.length - 1; i > 0; i--) {
    const randomIndex = Math.floor(Math.random() * (i + 1)); // Get a random index
    [shuffled[i], shuffled[randomIndex]] = [
      shuffled[randomIndex],
      shuffled[i],
    ]; // Swap elements
  }
  return shuffled;
}

function swicthColor(index: number){
  if(selectedColorIndex.value == null){
    selectedColorIndex.value = index;
  }else{
    const selectedIndex = selectedColorIndex.value;
    const copyColorToMatchArray = [...colorToMatchArray.value];
    const tempColor = copyColorToMatchArray[index];
    copyColorToMatchArray[index] = copyColorToMatchArray[selectedIndex];
    copyColorToMatchArray[selectedIndex] = tempColor;
    selectedColorIndex.value = null;
    colorToMatchArray.value = copyColorToMatchArray;
    countMatchColor();
  }
}

function countMatchColor(){
  let countMatch = 0;
  const colorArrayValue = colorArray.value;
  const colorToMatchArrayValue = colorToMatchArray.value;
  colorArrayValue.forEach((color, idx) => {
    if(colorToMatchArrayValue[idx] == color){
      countMatch++;
    }
  });
  countMatchRef.value = countMatch;
  if(countMatch == colorArrayValue.length){
    winMatchAction();
  }
}

function showToaster(){
  const toastLiveExample = document.getElementById('liveToast')
  if (toastLiveExample) {
    toastLiveExample.classList.add("show");
  }
}

function hideToaster(){
  const toastLiveExample = document.getElementById('liveToast')
  if (toastLiveExample) {
    toastLiveExample.classList.remove("show");
  }
}

function winMatchAction(){
 gameWin.value = true;
 showToaster();
}

function loseMatchAction(){
  gameWin.value = false;
  showToaster();
}

function retryGame(){
  gameStart.value = false;
  gameWin.value = false;
  countMatchRef.value = 0;
  hideToaster();
}
</script>

<template>
  <div class="container py-5">
    <div class="row justify-content-center">
      <div class="col-auto mb-4">
        <h4 class="w-auto mb-0">
          Matching Color Game
        </h4>
      </div>
    </div>
    <div class="row justify-content-center" v-if="!gameStart">
      <div class="col-9">
        <div class="row justify-content-between">
          <div v-for="(level, index) in difficultyButtons" :key="index" class="col-auto">
            <button class="btn btn-secondary" @click="onClickLevel(level.value)">{{ level.text }}</button>
          </div>
        </div>
      </div>
    </div>
    <div class="game-panel" v-if="gameStart">
      <div class="row justify-content-center" v-if="!gameWin">
        <div class="col-auto">
          <Timer @timer-end="loseMatchAction" initialTime="120" />
        </div>
      </div>
      <div class="row justify-content-center">
        <div class="col-auto">
          {{ countMatchRef }}
        </div>
      </div>
      <div class="row justify-content-center">
        <div class="col-auto px-2" v-for="(color, index) in colorToMatchArray" :key="index">
          <button class="btn btn-link fs-1 p-0" aria-label="Colored Trophy" @click="swicthColor(index)">
            <i class="bi bi-trophy position-absolute" :class="[selectedColorIndex != null && index == selectedColorIndex ? 'text-warning-emphasis' : 'text-dark']"></i>
            <i class="bi bi-trophy-fill" :style="{ color: color }"></i>
          </button>
        </div>
      </div>
      <div class="row justify-content-center">
        <div class="col-auto px-2" v-for="(color, index) in colorArray" :key="index">
          <button class="btn btn-link fs-1 p-0" aria-label="Colored Trophy">
            <i class="bi bi-trophy position-absolute text-dark"></i>
            <i class="bi bi-trophy-fill text-dark" :style="{ color: color }"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
  <div class="toast-container position-fixed bottom-50 end-50 p-3">
    <div id="liveToast" class="toast" role="alert" aria-live="assertive" aria-atomic="true">
      <div class="toast-header text-bg-primary">
        <strong class="me-auto">
          {{
            gameWin?'Congratulation':'Unfortunately'
          }}
        </strong>
      </div>
      <div class="toast-body text-bg-primary">
        <div class="row justify-content-center">
          <div class="col-12">
            {{
              gameWin?'You win the game. Wanna win another?':'You lose the game. Wanna try again?'
            }}
          </div>
          <div class="col-auto pt-4">
            <button class="btn btn-secondary" @click="retryGame()">LETSGO</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.toast-container{
  position: fixed;
  bottom: 50%;
  right: 50%;
  transform: translate(50%, 50%);
}
</style>
