<template>
  <div v-if="visible" class="popup-overlay">
    <div class="popup-box">
      <h2>Instrucciones - {{ juego }}</h2>
      <p v-if="juego === 'memoria'">
        Encuentra todas las parejas de cartas antes de que se acabe el tiempo.
      </p>
      <p v-else-if="juego === 'tres-cajas'">
        Adivina cuál caja tiene el premio. Solo tienes un intento.
      </p>
      <button @click="cerrar">Cerrar</button>
      <button @click="iniciar">Iniciar Juego</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    juego: String,
    visible: Boolean
  },
  emits: ['cerrar', 'iniciar'],
  methods: {
    cerrar() {
      this.$emit('cerrar')
    },
    iniciar() {
      this.$emit('iniciar')
    }
  }
}
</script>

<style scoped>
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.6);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}

.popup-box {
  background: white;
  padding: 20px;
  border-radius: 10px;
  width: 80%;
  max-width: 400px;
  text-align: center;
}
</style>
