<template>
  <div>
    <component :is="componenteJuego" />
  </div>
</template>

<script>
import JuegoMemoria from '../components/Memory.vue'
import JuegoTresCajas from '../components/Boxes.vue'

export default {
  name: 'Juego',
  components: {
    JuegoMemoria,
    JuegoTresCajas
  },
  computed: {
    componenteJuego() {
      const juego = this.$route.params.juego
      if (juego === 'memoria') return 'JuegoMemoria'
      if (juego === 'tres-cajas') return 'JuegoTresCajas'
      return null
    }
  }
}
</script>
