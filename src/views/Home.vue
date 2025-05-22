<template>
  <div class="home-container">
    <div class="contenido">
      <img src="/images/titulohome.png" alt="Título del juego" class="titulo-img" />

      <div class="texto">
        <h2>¡VAMOS A JUGAR!</h2>
        <p>Selecciona un juego y comencemos.</p>
      </div>

      <div class="botones">
        <button class="btn btn-amarillo" @click="mostrarInstrucciones('tres-cajas')">
          Encuentra la burger
        </button>
        <button class="btn btn-celeste" @click="mostrarInstrucciones('memoria')">
          Memoriza tu combo
        </button>
      </div>
    </div>

    <PopUpInstrucciones 
      :juego="juegoSeleccionado" 
      :visible="mostrarPopup" 
      @cerrar="cerrarPopup"
      @iniciar="iniciarJuego" 
    />
  </div>
</template>

<script>
import PopUpInstrucciones from '../components/Instructions.vue'

export default {
  components: {
    PopUpInstrucciones
  },
  data() {
    return {
      juegoSeleccionado: '',
      mostrarPopup: false
    }
  },
  methods: {
    mostrarInstrucciones(juego) {
      this.juegoSeleccionado = juego
      this.mostrarPopup = true
    },
    cerrarPopup() {
      this.mostrarPopup = false
    },
    iniciarJuego() {
      this.mostrarPopup = false
      this.$router.push(`/juego/${this.juegoSeleccionado}`)
    }
  }
}
</script>

<style scoped>
.home-container {
  background-image: url('/images/fondo.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.contenido {
  width: 100%;
  max-width: 45vh;
  padding: 0.5rem 0.5rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-between;
  height: 70vh;
  box-sizing: border-box;
  text-align: center;
  color: white;
  margin-top: -10vh;
}

.titulo-img {
  width: 100%;
}

.texto h2 {
  font-size: 5.5rem;
  font-weight: bold;
  margin: 1.5rem 0 0.5rem;
}

.texto p {
  font-size: 2.5rem;
  margin-bottom: 2rem;
}

.botones {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 4rem;
  align-items: center; /* Alinea al centro */
}

.btn {
  width: 100%;           /* Que no se limite al 100% del contenedor si este es pequeño */
  height: 10rem;         /* Fija la altura */
  font-size: 3rem;
  font-weight: bold;
  border: none;
  border-radius: 10px;
  cursor: pointer;
}

.btn-amarillo {
  background-color: #f7b733;
  color: black;
}

.btn-celeste {
  background-color: #00aaff;
  color: black;
}

/* Responsivo para celulares pequeños */
@media (max-width: 480px) {
  .contenido {
    height: 100vh;
    padding: 1.5rem 1rem;
  }

  .titulo-img {
    max-width: 90%;
  }

  .texto h2 {
    font-size: 1.5rem;
  }

  .texto p {
    font-size: 0.95rem;
  }

  .btn {
    font-size: 0.95rem;
    padding: 1rem;
  }
}
</style>
