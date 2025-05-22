<template>
  <div class="game-container">
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

    <div class="controls">
      <button @click="restartGame" :disabled="isShuffling">Reiniciar</button>
      <div v-if="result !== null" class="result">
        {{ result ? '¡Correcto!' : 'Incorrecto, intenta de nuevo.' }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import gsap from 'gsap'

const revealAtStart = ref(true)
const cupRefs = ref([])
const cups = ref([0, 1, 2]) // Mapeo de posiciones visuales
const ballIndex = ref(Math.floor(Math.random() * 3)) // Índice inicial de la bola
const showBall = ref(false)
const isShuffling = ref(false)
const result = ref(null)

// Posiciones horizontales en vw
const positions = ['10vw', '40vw', '70vw']

onMounted(() => {
  // Mostrar la bola al inicio antes de la mezcla
  revealAtStart.value = true;
  cups.value.forEach((pos, i) => {
    gsap.set(cupRefs.value[i], {
      x: positions[pos],
      y: -40,
    });
  });

  // Esperamos un tiempo para mostrar la bola y luego iniciamos la mezcla
  setTimeout(() => {
    revealAtStart.value = false; // Ocultar la bola
    liftAll(false); // Bajar los vasos
    setTimeout(() => {
      shuffleCups(); // Mezclar los vasos
    }, 500); // Tiempo para que la animación de los vasos se complete
  }, 1500); // Esperar antes de comenzar el ciclo de mezcla
});

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

    // Intercambiar lógicamente las posiciones de los vasos
    const temp = cups.value[i]
    cups.value[i] = cups.value[j]
    cups.value[j] = temp

    // Animar los vasos
    const tl = gsap.timeline()
    tl.to(cupRefs.value[i], {
      x: positions[cups.value[i]],
      duration: 0.4,
      ease: 'power2.inOut'
    })
    tl.to(cupRefs.value[j], {
      x: positions[cups.value[j]],
      duration: 0.4,
      ease: 'power2.inOut'
    }, '<') // Animación en paralelo

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
  if (isShuffling.value || showBall.value) return; // No hacer nada mientras se mezclan los vasos o si ya se mostró la bola

  // Verificar si el vaso es el correcto
  if (cups.value[index] === ballIndex.value) {
    // Si es el vaso correcto, asignar el index de la bola
    ballIndex.value = cups.value[index];
  }

  // Levantar el vaso después de hacer clic
  gsap.to(cupRefs.value[index], {
    y: -40, // Levantar el vaso
    duration: 0.3,
    ease: 'power1.out',
    onComplete: () => {
      // Después de que el vaso se levante, se muestra la bola
      showBall.value = true;

      // Verificar si la selección es correcta después de mostrar la bola
      result.value = cups.value[index] === ballIndex.value; // Verificar si el vaso seleccionado tiene la bola
    }
  });
}

function restartGame() {
  showBall.value = false;
  result.value = null;  // Reiniciar el resultado antes de la mezcla
  isShuffling.value = false;
  ballIndex.value = Math.floor(Math.random() * 3); // Elegir la posición aleatoria de la bola
  cups.value = [0, 1, 2]; // Restablecer las posiciones originales de los vasos

  // Levantar los vasos y mostrar la bola al inicio
  revealAtStart.value = true; // Mostrar la bola de nuevo

  cupRefs.value.forEach((cup, i) => {
    gsap.set(cup, {
      x: positions[cups.value[i]],
      y: -40,
    });
  });

  // Asegurarse de que la bola esté en el vaso correcto antes de comenzar la mezcla
  setTimeout(() => {
    liftAll(false); // Bajar los vasos

    // Después de levantar los vasos, ocultamos la bola para la mezcla
    setTimeout(() => {
      revealAtStart.value = false; // Ocultar la bola
      shuffleCups(); // Iniciar la mezcla de los vasos
    }, 500); // Asegura que la bola se oculte un poco después de que los vasos bajen
  }, 1500); // Espera a que los vasos se levanten primero
}
</script>

<style scoped>
.game-container {
  width: 100vw;
  height: 100vh;
  background: #f1f1f1;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.cups {
  position: relative;
  width: 100%;
  height: 200px;
  margin-bottom: 30px;
}

.cup {
  position: absolute;
  bottom: 0;
  width: 60px;
  height: 100px;
  background-color: #8b5e3c;
  border-radius: 0 0 30px 30px;
  cursor: pointer;
  display: flex;
  align-items: flex-end;
  justify-content: center;
}

.ball {
  width: 20px;
  height: 20px;
  background-color: red;
  border-radius: 50%;
  margin-bottom: 10px;
}

.controls {
  text-align: center;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
}

.result {
  margin-top: 20px;
  font-size: 18px;
  font-weight: bold;
}
</style>
