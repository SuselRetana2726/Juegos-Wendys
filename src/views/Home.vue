<template>
    <div>
        <h1>Selecciona un juego</h1>
        <button @click="mostrarInstrucciones('memoria')">🧠 Juego de Memoria</button>
        <button @click="mostrarInstrucciones('tres-cajas')">🎁 Juego de Tres Cajas</button>

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
