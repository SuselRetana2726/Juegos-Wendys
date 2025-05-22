<template>
  <div>
    <h2>🧠 Juego de Memoria</h2>
    <p>Tiempo restante: {{ tiempo }} segundos</p>
    <button @click="reiniciarJuego">Reiniciar Juego</button>

    <div class="tablero">
      <div 
        v-for="(carta, index) in cartas" 
        :key="index" 
        class="carta"
        @click="voltearCarta(index)"
      >
        <div class="contenido" :class="{ volteada: carta.volteada || carta.encontrada }">
          {{ carta.volteada || carta.encontrada ? carta.valor : '?' }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tiempo: 30,
      timerId: null,
      cartas: [],
      primeraCartaIndex: null,
      segundaCartaIndex: null,
      bloquearTablero: false,
      parejasEncontradas: 0
    }
  },
  created() {
    this.iniciarTemporizador()
    this.generarCartas()
  },
  beforeUnmount() {
    clearInterval(this.timerId)
  },
  methods: {
    iniciarTemporizador() {
      this.tiempo = 30
      this.timerId = setInterval(() => {
        if (this.tiempo > 0) {
          this.tiempo--
        } else {
          clearInterval(this.timerId)
          alert('Se acabó el tiempo, perdiste 😞')
          this.reiniciarJuego()
        }
      }, 1000)
    },
    generarCartas() {
      // Valores: pares del 1 al 6 para total 12 cartas
      let valores = [1,2,3,4,5,6]
      // Duplicar valores para hacer pares
      valores = valores.concat(valores)
      // Barajar las cartas
      this.cartas = valores
        .map(valor => ({ valor, volteada: false, encontrada: false }))
        .sort(() => Math.random() - 0.5)
    },
    voltearCarta(index) {
      if (this.bloquearTablero) return
      let carta = this.cartas[index]
      if (carta.volteada || carta.encontrada) return
      
      this.cartas[index].volteada = true

      if (this.primeraCartaIndex === null) {
        this.primeraCartaIndex = index
      } else {
        this.segundaCartaIndex = index
        this.bloquearTablero = true
        this.revisarPareja()
      }
    },
    revisarPareja() {
      const primeraCarta = this.cartas[this.primeraCartaIndex]
      const segundaCarta = this.cartas[this.segundaCartaIndex]

      if (primeraCarta.valor === segundaCarta.valor) {
        // Pareja encontrada
        this.cartas[this.primeraCartaIndex].encontrada = true
        this.cartas[this.segundaCartaIndex].encontrada = true
        this.parejasEncontradas++
        this.resetTurno()
        if (this.parejasEncontradas === 6) {
          clearInterval(this.timerId)
          alert('¡Ganaste! 🎉')
          // Aquí puedes disparar evento o mostrar popup ganador
        }
      } else {
        // No es pareja, voltearlas de nuevo después de un segundo
        setTimeout(() => {
          this.cartas[this.primeraCartaIndex].volteada = false
          this.cartas[this.segundaCartaIndex].volteada = false
          this.resetTurno()
        }, 1000)
      }
    },
    resetTurno() {
      this.primeraCartaIndex = null
      this.segundaCartaIndex = null
      this.bloquearTablero = false
    },
    reiniciarJuego() {
      clearInterval(this.timerId)
      this.parejasEncontradas = 0
      this.generarCartas()
      this.iniciarTemporizador()
      this.resetTurno()
    }
  }
}
</script>

<style scoped>
.tablero {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-template-rows: repeat(4, 130px);
  gap: 10px;
  margin-top: 20px;
}

.carta {
  background: #ccc;
  border-radius: 8px;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 40px;
  user-select: none;
  box-shadow: 0 0 5px #999;
  transition: transform 0.3s;
}

.contenido {
  width: 90px;
  height: 120px;
  background: #eee;
  border-radius: 8px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.volteada {
  background: #6cf;
  font-weight: bold;
  font-size: 60px;
}
</style>
