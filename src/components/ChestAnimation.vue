<template>
  <div class="chest-container animate__animated animate__lightSpeedInLeft">
    <div 
      v-if="isReveal"
    class="congrats"
    :class="{
        animate__animated: isReveal,
        animate__tada: isReveal,
        reveal: isReveal
    }"
    >
    <img :src="congrats" alt="congratulations" />
    </div>

    <div 
      class="chest"
      @click="openChest"
      :class="{ animate__animated: isSpinning, animate__rubberBand: isSpinning }" 
      >
      <img :src="chestImage" alt="Magic Chest" />
    </div>

    <button v-if="isOpen" class="reset-btn" @click="resetChest">
      Reset
    </button>
  </div>
</template>


<script setup>
import { ref } from 'vue'
import confetti from 'canvas-confetti'

// Import both chest images
import chestClosed from '../assets/chestClosed.png'
import chestOpen from '../assets/chestOpen.png'
import congrats from '../assets/congrats1.png'

const isSpinning = ref(false)
const isOpen = ref(false)
const chestImage = ref(chestClosed)
const isReveal = ref(false)

const victorySound = new Audio('/victorySound.mp3')

function openChest() {
  if (isOpen.value) return

  isSpinning.value = true
  setTimeout(() => {
    isSpinning.value = false
    isOpen.value = true
    chestImage.value = chestOpen
    victorySound.play()
    isReveal.value = true


    confetti({
      particleCount: 100,
      spread: 70,
      origin: { y: 0.6 }
    })
  }, 1000)
}

function resetChest() {
  isOpen.value = false
  chestImage.value = chestClosed
    isReveal.value = false // 👈 hides congratulations
}
</script>


<style scoped>
.chest-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.chest {
  width: 200px;
  height: 200px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
}

.chest img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  user-select: none;
  pointer-events: none;
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
.congrats img{
    width: 550px;
    height: auto;
}

</style>

