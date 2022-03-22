<template>
  <main class="game">
    <div class="game__modal" v-if="modal">
      <div class="modal">
        Przegrałeś! 😓 Twoje punkty: <span class="special">{{score}}</span>
        <button type="button" @click="reset">Zagraj jeszcze raz!</button>
      </div>
    </div>
    <div class="game__lives">
      <span :class="lives >= 1 ? '' : 'game__live--unactive'">&times;</span>
      <span :class="lives >= 2 ? '' : 'game__live--unactive'">&times;</span>
      <span :class="lives >= 3 ? '' : 'game__live--unactive'">&times;</span>
    </div>
    <div class="game__score">{{score}} 🏆</div>
    <div class="game__bonus">
      <div class="game__timer">10:00</div>
    </div>
    <ClientOnly>
    <div class="game__content">
      <h1>What's the hex?</h1>
      <div class="game__color-text">{{ goodHex }}</div>
      <div class="game__colors">
        <div class="game__color-card" 
        @click="checkHex"
        v-for="color in colors" 
        :key="color" 
        :data-hex="color"
        :style="`background-color: ${color}`" 
        ></div>
      </div>
    </div>
    </ClientOnly>
  </main>
</template>

<script setup>

let colors = ref([])
let goodHex = ref("")
let score = ref(0)
let lives = ref(3)
let modal = ref(false)

const generateHex = () =>{
  colors.value = [randomHex(), randomHex(),randomHex()]
  goodHex.value = colors.value[~~(Math.random() * colors.value.length)]
}
function randomHex(){
    return `#${Math.floor(Math.random()*16777215).toString(16).padStart(6, '0')}`
}
const checkHex = (event) =>{
  if(event.target.dataset.hex === goodHex.value) {
    win()
  }
  else {
    loose()
  }
  if(lives.value < 1){
    modal.value = true
  }else{
    generateHex()
  }
}
const win = () => {
  console.log("Wygrales!")
  score.value++
}
const loose = () => {
  console.log("Przegrales!")
  lives.value--
}
const reset = () =>{
  lives.value = 3
  modal.value = false
  score.value = 0
  generateHex()
}
generateHex()

useMeta({
  title: computed(() => `What's the hex? | Game` )
})
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@200;300;400;500;600;700;900&display=swap');

*, *::after, *::before{
  box-sizing: border-box;
}
h1{
  color: #fff;
}
.game__modal{
  position: fixed;
  width: 100%;
  height: 100vh;
  backdrop-filter: blur( 4px );
  z-index: 2;
  display: flex;
  justify-content: center;
  align-items: center;
}
.modal{
  display: flex;
  flex-direction: column;
  gap: 3em;
  padding: 2em;
  background-color: #1d173a;
  button{
    padding: 0.5em 1em;
    font-family: "Poppins", sans-serif;
    font-weight: bold;
    border-radius: 10px;
    border: none;
    background-color: rgb(108, 255, 95);
    transition: transform 0.3s;
    cursor:pointer;
    &:hover, &:focus{
      transition: transform 0.3s;
      transform: scale(0.95);
    }
  }
}
.game__score{
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  top: 3em;
  color: #fae636;
}
.special{
  color: #fae636;
  font-size: 3em;
  text-align: center;
  font-weight: bold;
}
.game__colors{
  display: flex;
  gap: 1em;
}
.game__color-text{
  font-size: 2em;
  font-weight: bold;
}
.game__color-card{
  width: 4em;
  height: 4em;
  border-radius: 5px;
  background-color: transparent;
  border: 2px solid rgba(0,0,0,0.8);
  box-sizing: content-box;
  cursor: pointer;
}
.game__content{
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  gap: 2em;
  width: 100%;
  height: 100vh;
}
.game__lives{
  position: absolute;
  top: 1em;
  left: 1em;
  font-size: 2em;
  padding: 0.2em 0.5em;
  color: #eb6767;
  background-color: rgba(0,0,0,0.1);
  border-radius: 20px;
}
.game__live--unactive{
  color: rgb(145, 145, 145);
}
.game__bonus{
  position: absolute;
  top: 3em;
  right: 3em;
}
body{
  font-family: "Poppins", sans-serif;
  background: linear-gradient(-45deg, #1d173a, #0c2736, #070224);
	background-size: 400% 400%;
	animation: gradient 15s ease infinite;
  width: 100%;
  height: 100vh;
  color: #fff;
}
@keyframes gradient {
	0% {
		background-position: 0% 50%;
	}
	50% {
		background-position: 100% 50%;
	}
	100% {
		background-position: 0% 50%;
	}
}
</style>