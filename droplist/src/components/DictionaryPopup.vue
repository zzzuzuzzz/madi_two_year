<template>
  <div class="dictionary-popup">
    <div class="dictionary-content">
      <h2>Словарь элементов</h2>
      <ul>
        <li v-for="element in elements" :key="element.id">
          {{ element.id }} - {{ element.name }}
        </li>
      </ul>
      <button @click="close">Закрыть</button>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  elements: {
    type: Array,
    required: true
  }
});

const emit = defineEmits(['close']);

const close = () => {
  emit('close');
};
</script>

<style scoped>
.dictionary-popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1001;
}

.dictionary-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  max-width: 500px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 5px;
}
</style>