<template>
  <div class="game-container">
    <!-- 🧠 Scoreboard shows wins and attempts -->
    <p class="scoreboard">🏆 Wins: {{ wins }} / Attempts: {{ attempts }}</p>

    <!-- 🎉 Show congratulations image if the user wins -->
    <div 
      v-if="isReveal"
      class="congrats animate__animated animate__tada"
    >
      <img :src="congrats" alt="Congratulations" />
    </div>

    <!-- 🏴‍☠️ Pirate reactions -->
    <div v-if="pirateReaction.visible" class="pirate-popup animate__animated" :class="pirateReaction.animation">
      <img :src="pirateReaction.image" alt="Pirate Reaction" />
        <div class="speech-bubble">
         <p class="reaction-text">{{ pirateReaction.text }}</p>
    </div>
    </div>

    <!-- 💼 The chest grid (3 chests side-by-side) -->
    <div class="glass-card animate__animated animate__rubberBand">
      <!-- 🎯 Message appears when user clicks a chest -->
      <p v-if="message" class="message">{{ message }}</p>
      <span class="glass-shine"></span>
      <div class="chest-grid">
        <div
          v-for="(chest, index) in chests"
          :key="index"
          class="chest"
          :class="{
            animate__animated: clickedIndex === index,
            animate__shakeX: clickedIndex === index && !isWinner(index),
            animate__tada: clickedIndex === index && isWinner(index)
          }"
          @click="handleClick(index)"
        >
          <img :src="getChestImage(index)" alt="Chest" class="chest-image"/>
        </div>
      </div>
    </div>

    <!-- 🔁 Show reset button only after winning -->
    <button v-if="isReveal" class="reset-btn" @click="resetGame">
      Reset Game
    </button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import 'animate.css'
import confetti from 'canvas-confetti'
import chestClosed from '../assets/chestClosed.png'
import chestOpen from '../assets/chestOpen.png'
import congrats from '../assets/congrats1.png'
import pirateHappy from '../assets/crew2.png'
import pirateAngry from '../assets/crew1.png'

const totalChests = 3
const chests = ref(Array(totalChests).fill(null))
const chestImage = ref(chestClosed)
const winningIndex = ref(null)
const clickedIndex = ref(null)
const message = ref('')
const isReveal = ref(false)
const wins = ref(0)
const attempts = ref(0)
const victorySound = new Audio('victorySound.mp3')
const failSound = new Audio('failSound.wav')

const pirateReaction = ref({
  visible: false,
  image: '',
  text: '',
  animation: 'animate__fadeInLeft'
})

function setWinningChest() {
  winningIndex.value = Math.floor(Math.random() * totalChests)
  message.value = ''
  clickedIndex.value = null
  isReveal.value = false
  pirateReaction.value.visible = false
}

function handleClick(index) {
  if (isReveal.value) return
  clickedIndex.value = index
  attempts.value++

  if (index === winningIndex.value) {
    chestImage.value = chestOpen
    message.value = '🎉 You found the treasure!'
    isReveal.value = true
    wins.value++
    victorySound.play()
    confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } })

    pirateReaction.value = {
      visible: true,
      image: pirateHappy,
      text: 'Arrr! Ye found it, matey! 🍻',
      animation: 'animate__bounceInRight'
    }
  } else {
    failSound.play()
    message.value = '❌ Try again!'

    pirateReaction.value = {
      visible: true,
      image: pirateAngry,
      text: 'Ye scallywag! That ain’t it! 😡',
      animation: 'animate__shakeX'
    }
  }
}

function getChestImage(index) {
  return isReveal.value && index === winningIndex.value ? chestOpen : chestClosed
}

function isWinner(index) {
  return index === winningIndex.value
}

function resetGame() {
  setWinningChest()
}

onMounted(() => {
  setWinningChest()
})
</script>

<style scoped>
.game-container {
  text-align: center;
  padding: 0 20px;
  color: white;
  margin: 0;
}
.scoreboard {
  font-size: 1.9rem;
  margin-bottom: 6px;
}
.message {
  font-size: 1.1rem;
  margin-bottom: 8px;
}
.chest-grid {
  display: flex;
  justify-content: center;
  gap: 30px;
  margin-top: 10px;
}
.chest {
  width: 200px;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
.chest-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
.chest-image:hover {
  filter: drop-shadow(0 0 12px rgba(255, 215, 0, 0.9));
  transform: scale(1.05);
}
.congrats img {
  width: 550px;
  height: auto;
  margin-top: 20px;
}
.reset-btn {
  margin-top: 20px;
  padding: 10px 20px;
  background-color: #ffd700;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.3s ease;
}
.reset-btn:hover {
  background-color: #ffb700;
}
.glass-card {
  position: relative;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
  backdrop-filter: blur(20px) saturate(180%);
  -webkit-backdrop-filter: blur(20px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.25);
  padding: 20px 30px;
  max-width: 520px;
  margin: 0 auto;
  overflow: hidden;
  z-index: 1;
}
.glass-card::before {
  content: "";
  position: absolute;
  top: 0; left: 0;
  width: 100%;
  height: 100%;
  background: rgba(21, 1, 76, 0.419);
  z-index: 0;
}
.glass-card > * {
  position: relative;
  z-index: 1;
}
.pirate-reaction {
  position: absolute;
  bottom: 60px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(0, 0, 0, 0.6);
  padding: 10px 20px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  color: #fffdd0;
  font-weight: bold;
  z-index: 10;
}
.pirate-reaction img {
  height: 80px;
  width: auto;
  border-radius: 8px;
}
.reaction-text {
  font-size: 1rem;
  max-width: 200px;
}

.pirate-popup {
  position: absolute;
  bottom: 100px;
  left: 80%;
  transform: translateX(-50%);
  display: flex;
  align-items: flex-start;
  gap: 12px;
  animation: pop-up 0.5s ease-out forwards;
  z-index: 20;
  pointer-events: none;
}

/* 🧑‍🎤 Pirate Image */
.pirate-popup img {
  width: 180px;
  height: auto;
  border-radius: 10px;
  z-index: 1;
}

.pirate-popup p {
  margin-top: 8px;
  color: #fffacd;
  font-style: italic;
  font-size: 1.1rem;
}


@keyframes pop-up {
  0% {
    transform: translate(-50%, 100%);
    opacity: 0;
  }
  60% {
    transform: translate(-50%, -10%);
    opacity: 1;
  }
  100% {
    transform: translate(-50%, 0%);
    opacity: 1;
  }
}

/* 💬 Comic-style Bubble */
.speech-bubble {
  position: relative;
  background: #fffdd0;
  color: #222;
  padding: 12px 16px;
  border-radius: 15px;
  font-size: 1.05rem;
  font-weight: bold;
  max-width: 240px;
  line-height: 1.4;
  box-shadow: 2px 4px 10px rgba(0, 0, 0, 0.3);
}

/* 🗨️ Bubble tail */
.speech-bubble::after {
  content: '';
  position: absolute;
  top: 20px;
  left: -15px;
  width: 0;
  height: 0;
  border: 10px solid transparent;
  border-right-color: #fffdd0;
  transform: rotate(-10deg);
}
::v-deep(.speech-bubble p) {
  color: #222;
}


</style>
