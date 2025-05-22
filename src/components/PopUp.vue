<!-- src/components/PopUp.vue -->
<template>
  <div class="popup" v-if="visible">
    <div class="popup-content">
      <slot />
      <div class="popup-actions">
        <button v-if="onClose" @click="onClose">Cerrar</button>
        <button v-if="onConfirm" @click="onConfirm">Iniciar</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    visible: Boolean,
    onClose: Function,
    onConfirm: Function
  }
}
</script>

<style scoped>
.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0,0,0,0.6);
  display: flex;
  justify-content: center;
  align-items: center;
}

.popup-content {
  background: white;
  padding: 20px;
  border-radius: 12px;
  max-width: 90%;
}
</style>
