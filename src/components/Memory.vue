<template>
  <div class="juego-memoria">
    <img src="/images/fondo.png" alt="Fondo" class="fondo" />

    <div class="contenido">
      <h2>MEMORIZA TU COMBO</h2>

      <div class="temporizador">
        <img src="/icons/reloj.svg" alt="Reloj" class="icono-reloj" />
        <span>{{ tiempo }}</span>
      </div>

      <div class="tablero">
        <div v-for="(carta, index) in cartas" :key="index" class="carta" @click="voltearCarta(index)"
          :class="{ mezclando: mezclando }" :style="mezclando ? `animation-delay: ${index * 0.05}s` : ''">
          <div class="card-inner" :class="{ girada: carta.volteada || carta.encontrada }">
            <div class="card-front">
              <img src="/images/card.png" alt="Reverso" />
            </div>
            <div class="card-back">
              <img :src="`/images/${carta.valor}.png`" :alt="carta.valor" />
            </div>
          </div>
        </div>
      </div>

      <p v-if="ganaste" class="mensaje-victoria">🎉 ¡Ganaste! 🎉</p>
      <p v-if="perdiste" class="mensaje-derrota">😞 Se acabó el tiempo. ¡Inténtalo de nuevo!</p>

      <div class="acciones">
        <button @click="reiniciarJuego" class="boton-icono boton-home">
          <img src="/icons/casa.svg" alt="Inicio" />
        </button>
        <button class="boton-icono boton-info">
          <img src="/icons/info.svg" alt="Información" />
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cartas: [],
      primeraCarta: null,
      segundaCarta: null,
      bloqueo: false,
      tiempo: 30,
      temporizador: null,
      ganaste: false,
      perdiste: false,
      mezclando: false
    };
  },
  created() {
    this.reiniciarJuego();
  },
  methods: {
    generarCartas() {
      const valoresBase = ['hamburguesa1', 'hamburguesa2', 'Llama', 'logo', 'personaje', 'titulo'];
      const valores = [...valoresBase, ...valoresBase];
      const mezcladas = valores
        .map(valor => ({ valor, volteada: false, encontrada: false }))
        .sort(() => 0.5 - Math.random());
      return mezcladas;
    },
    voltearCarta(index) {

      if (this.ganaste || this.perdiste) return;
      const carta = this.cartas[index];
      if (this.bloqueo || carta.volteada || carta.encontrada) return;

      carta.volteada = true;

      if (!this.primeraCarta) {
        this.primeraCarta = { carta, index };
      } else if (!this.segundaCarta) {
        this.segundaCarta = { carta, index };
        this.bloqueo = true;

        setTimeout(() => {
          const { carta: primera } = this.primeraCarta;
          const { carta: segunda } = this.segundaCarta;

          if (primera.valor === segunda.valor) {
            primera.encontrada = true;
            segunda.encontrada = true;
          } else {
            primera.volteada = false;
            segunda.volteada = false;
          }

          this.primeraCarta = null;
          this.segundaCarta = null;
          this.bloqueo = false;

          if (this.cartas.every(c => c.encontrada)) {
            this.ganaste = true;
            clearInterval(this.temporizador);
          }
        }, 1000);
      }
    },
    iniciarTemporizador() {
      this.temporizador = setInterval(() => {
        if (this.tiempo > 0) {
          this.tiempo--;
        } else {
          clearInterval(this.temporizador);
          this.perdiste = true;
        }
      }, 1000);
    },
    reiniciarJuego() {
      this.cartas = this.generarCartas();
      this.primeraCarta = null;
      this.segundaCarta = null;
      this.bloqueo = true;
      this.tiempo = 40;
      this.ganaste = false;
      this.perdiste = false;
      clearInterval(this.temporizador);

      this.mezclando = true;
      setTimeout(() => {
        this.mezclando = false;
        this.bloqueo = false;
        this.iniciarTemporizador();
      }, 1500);
    }
  }
};
</script>

<style scoped>
.juego-memoria {
  width: 100vw;
  height: 100vh;
  position: relative;
  overflow: hidden;
}

.fondo {
  position: absolute;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: 0;
}

.contenido {
  position: relative;
  z-index: 1;
  padding: 20px;
  text-align: center;
  color: white;
  font-weight: bold;
}

.temporizador {
  background: #00bfff;
  color: #000;
  display: inline-flex;
  align-items: center;
  gap: 5px;
  padding: 5px 10px;
  border-radius: 8px;
  margin: 10px 0;
}

.icono-reloj {
  width: 20px;
  height: 20px;
}

.tablero {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  grid-template-rows: repeat(4, 100px);
  gap: 10px;
  justify-content: center;
  margin: 20px auto;
}

.carta {
  perspective: 800px;
  width: 100px;
  height: 100px;
}

/* Animación de mezcla tipo truco de manos */
.mezclando {
  animation: mezclar 0.6s ease-in-out forwards;
}

@keyframes mezclar {
  0% {
    transform: translateY(0) scale(1);
  }

  30% {
    transform: translateY(-20px) scale(1.1) rotate(5deg);
  }

  60% {
    transform: translateY(20px) scale(0.9) rotate(-5deg);
  }

  100% {
    transform: translateY(0) scale(1);
  }
}

.card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  transition: transform 0.6s ease;
  transform-style: preserve-3d;
}

.card-inner.girada {
  transform: rotateY(180deg);
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border-radius: 10px;
}

.card-front img,
.card-back img {
  width: 100%;
  height: 100%;
  object-fit: contain;
  border-radius: 10px;
}

.card-back {
  transform: rotateY(180deg);
}

.mensaje-victoria,
.mensaje-derrota {
  font-size: 18px;
  margin-top: 10px;
}

.mensaje-victoria {
  color: #00ff00;
}

.mensaje-derrota {
  color: #ff5050;
}

.acciones {
  position: absolute;
  bottom: 4vh;
  left: 50%;
  transform: translateX(-50%);
  display: flex;
  gap: 4vw;
  z-index: 2;
}

.boton-icono {
  background-color: white;
  border: none;
  border-radius: 50%;
  width: clamp(50px, 6vw, 80px);
  height: clamp(50px, 6vw, 80px);
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
  width: 50%;
  height: 50%;
  object-fit: contain;
}

.boton-home {
  position: absolute;
  bottom: 4vh;
  left: 4vw;
}

.boton-info {
  position: absolute;
  bottom: 4vh;
  right: 4vw;
}
</style>
