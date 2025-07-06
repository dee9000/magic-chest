<template>
<div class="title">
      <h1 class="game-title animate__animated animate__fadeInDown">
    ⚓
      <img src="/src/assets/title2.png" alt="pirate icons" class="pirate-banner" />
    🏴‍☠️
  </h1>
</div>

<div class="app-container">
  <!--<ChestAnimation/>-->
  <MemoryChestGame />
<p class="game-subheading">
  {{ rotatingHeading }}
</p>

</div>
</template>


<script setup>
// 🧠 Vue imports
import { ref, onMounted, onBeforeUnmount } from 'vue'
import MemoryChestGame from './components/MemoryChestGame.vue'

// 🏴‍☠️ Subheadings Array
const pirateHeadings = [
  "💣 One chest hides the treasure, the rest be cursed!",
  "🏝️ Only one way to the booty, matey!",
  "☠️ Choose wisely... or face the Kraken!",
  "🪙 A pirate's luck be tested!",
  "⚓ The treasure awaits, if ye dare!",
  "🧭 Trust yer instincts, not yer eyes!",
  "🦜 Pirates, ye beware… Arrrgh! Only one chest holds yer fortune!",
  "🪝 Three chests. One prize. Pick wisely... or face the curse!",
  "🏴‍☠️ The loot is near — but so is doom! Choose yer fate, pirate!",
  "⚔️ One chest holds the treasure… the others, a pirate’s shame.",
  "👑 Arrr! Only the cleverest scallywags find the gold!"
]

// 🔁 Reactive heading rotation
const currentHeadingIndex = ref(0)
const rotatingHeading = ref(pirateHeadings[0])
let headingInterval = null

onMounted(() => {
  headingInterval = setInterval(() => {
    currentHeadingIndex.value = (currentHeadingIndex.value + 1) % pirateHeadings.length
    rotatingHeading.value = pirateHeadings[currentHeadingIndex.value]
  }, 15000)
})

onBeforeUnmount(() => {
  clearInterval(headingInterval)
})
</script>


<style>
/* ---------- Global Styles ---------- */
body {
  margin: 0;
  font-family: 'Segoe UI', sans-serif;
  background-color: #1a1a1a;
}

/* ---------- Top Title Banner ---------- */
.title {
  width: 100%;
  text-align: center;
  padding: 10px 20px 0;
  margin: 0 auto;
}

.game-title {
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 2rem;
  font-family: 'Pirata One', cursive;
  color: gold;
  text-shadow: 2px 2px 10px black;
  margin-bottom: 1rem;
}

.pirate-banner {
  display: block;
  max-width: 600px;
  height: auto;
  margin: -250px auto -300px;
  padding-top: 1rem;
}

/* ---------- Main Container for Game + Subheading ---------- */
.app-container {
  display: flex;
  flex-direction: column; /* stack game + subheading vertically */
  justify-content: center;
  align-items: center;
  min-height: calc(100vh - 150px); /* room for banner at top */
  padding: 1rem 20px;
}

/* ---------- Rotating Subheading ---------- */
.game-subheading {
  font-size: 1.4rem;
  color: #fffbcc;
  font-style: italic;
  margin-top: 2rem;
  animation: fade-in 1s ease-in-out;
  transition: all 0.5s ease;
  text-align: center;
}

/* ---------- Animation ---------- */
@keyframes fade-in {
  from {
    opacity: 0;
    transform: translateY(-10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}



</style>