<template>
  <div class="game-container">
    <div class="title">Encuentra la<br>burger</div>

    <div class="cups">
      <div
        v-for="(cup, index) in 3"
        :key="index"
        class="cup"
        :ref="el => cupRefs[index] = el"
        @click="handleGuess(index)"
      >
        <div
          class="ball"
          v-if="(revealAtStart || showBall) && index === ballIndex"
        ></div>
      </div>
    </div>
    <div class="acciones">
      <button @click="home" class="boton-icono boton-home">
        <img src="/icons/casa.svg" alt="Inicio" />
      </button>
      <button @click="mostrarInstrucciones('tres-cajas')" class="boton-icono boton-info">
          <img src="/icons/info.svg" alt="Información" />
      </button>
    </div>

    <PopUpInstrucciones 
      juego="tres-cajas" 
      :visible="mostrarPopup" 
      @cerrar="cerrarInstrucciones"
      @iniciar="restartGame" />

      <PopUpGanaste
      juego="tres-cajas" 
      :visible="result !== null ? result : false" 
      @iniciar="salir"
    />

    <PopUpPerdiste
      juego="tres-cajas"
      :visible="result !== null ? !result : false" 
      @cerrar="salir"
      @iniciar="restartGame"
    />

    <!-- <div class="controls">
      <button @click="restartGame" :disabled="isShuffling">Reiniciar</button>
      <div v-if="result !== null" class="result">
        {{ result ? '¡Correcto!' : 'Incorrecto, intenta de nuevo.' }}
      </div>
    </div> -->
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import gsap from 'gsap'
import { useRouter } from 'vue-router'

import PopUpInstrucciones from '../components/Instructions.vue'
import PopUpGanaste from '../components/PopUpWin.vue'
import PopUpPerdiste from '../components/PopUpLose.vue'

const revealAtStart = ref(true)
const cupRefs = ref([])
const cups = ref([0, 1, 2])
const ballIndex = ref(Math.floor(Math.random() * 3))
const showBall = ref(false)
const isShuffling = ref(false)
const result = ref(null)
const mostrarPopup = ref(false)

const router = useRouter()
const positions = ['20%', '50%', '80%']

onMounted(() => {
  revealAtStart.value = true
  cups.value.forEach((pos, i) => {
    gsap.set(cupRefs.value[i], {
      left: positions[pos],
      y: -40,
    })
  })

  setTimeout(() => {
    revealAtStart.value = false
    liftAll(false)
    setTimeout(() => {
      shuffleCups()
    }, 500)
  }, 1500)
})

function mostrarInstrucciones() {
  mostrarPopup.value = true
}

function cerrarInstrucciones() {
  mostrarPopup.value = false
}

function salir() {
  result.value=null;
  router.push('/')
}

function home() {
  router.push('/')
}

function liftAll(up = true) {
  cupRefs.value.forEach(cup => {
    gsap.to(cup, {
      y: up ? -40 : 0,
      duration: 0.4,
      ease: 'power2.out'
    })
  })
}

function shuffleCups() {
  isShuffling.value = true
  let swaps = 5
  let count = 0

  const doShuffle = () => {
    const i = Math.floor(Math.random() * 3)
    let j
    do {
      j = Math.floor(Math.random() * 3)
    } while (j === i)

    const temp = cups.value[i]
    cups.value[i] = cups.value[j]
    cups.value[j] = temp

    const tl = gsap.timeline()
    tl.to(cupRefs.value[i], {
      left: positions[cups.value[i]],
      duration: 0.4,
      ease: 'power2.inOut'
    })
    tl.to(cupRefs.value[j], {
      left: positions[cups.value[j]],
      duration: 0.4,
      ease: 'power2.inOut'
    }, '<')

    count++
    if (count < swaps) {
      setTimeout(doShuffle, 600)
    } else {
      setTimeout(() => {
        isShuffling.value = false
      }, 600)
    }
  }

  doShuffle()
}

function handleGuess(index) {
  if (isShuffling.value || showBall.value) return

  if (cups.value[index] === ballIndex.value) {
    ballIndex.value = cups.value[index]
  }

  gsap.to(cupRefs.value[index], {
    y: -40,
    duration: 0.3,
    ease: 'power1.out',
    onComplete: () => {
      showBall.value = true
      result.value = cups.value[index] === ballIndex.value
    }
  })
}

function restartGame() {
  mostrarPopup.value=false
  showBall.value = false
  result.value = null
  isShuffling.value = false
  ballIndex.value = Math.floor(Math.random() * 3)
  cups.value = [0, 1, 2]
  revealAtStart.value = true

  cupRefs.value.forEach((cup, i) => {
    gsap.set(cup, {
      left: positions[cups.value[i]],
      y: -40,
    })
  })

  setTimeout(() => {
    liftAll(false)
    setTimeout(() => {
      revealAtStart.value = false
      shuffleCups()
    }, 500)
  }, 1500)
}
</script>

<style scoped>
.game-container {
  width: 100vw;
  height: 100vh;
  background-image: url('/images/fondo.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  box-sizing: border-box;
  overflow: hidden;
  position: relative;
}

.title {
  font-size: 4vh;
  color: white;
  font-weight: bold;
  text-align: center;
  text-transform: uppercase;
  margin-top: 6vh;
  margin-left: 3vw;
}

.cups {
  position: relative;
  width: 100%;
  height: 5vh;
  margin-top: 2vh;
}

.cup {
  width: 15vh;
  height: 15vh;
  background-image: url('/images/bolsas.png');
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center bottom;
  cursor: pointer;
  position: absolute;
  bottom: 0;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  transform: translateX(-50%);
}

.ball {
  width: 10vh;
  height: 10vh;
  background-image: url('/images/hamburguesa1.png');
  background-size: contain;
  background-repeat: no-repeat;
  background-position: center;
  margin-bottom: -8vh;
}

.controls {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
}

.result {
  margin-top: 16px;
  font-size: 20px;
  font-weight: bold;
  color: white;
  text-shadow: 1px 1px 2px black;
  text-align: center;
}

/* Botones fijos abajo */
.acciones {
  display: flex;
  justify-content: space-between;
  width: 70vw;
  margin: 3vh auto 5.5vh;
}

.boton-icono {
  background-color: white;
  border: none;
  border-radius: 50%;
  width: 8vh;
  height: 8vh;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.25);
  cursor: pointer;
  transition: transform 0.2s ease;
}

.boton-icono:hover {
  transform: scale(1.1);
}

.boton-icono img {
  width: 4vh;
  height: 4vh;
  object-fit: contain;
}
</style>
