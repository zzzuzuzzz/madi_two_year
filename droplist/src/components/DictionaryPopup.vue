<template>
  <div class="dictionary-popup">
    <div class="dictionary-content">
      <h2>Словарь элементов</h2>
      <ul>
        <li v-for="element in elements" :key="element.id">
          {{ element.id }} - {{ element.name }}
        </li>
      </ul>
      <button @click="$emit('close')">Закрыть</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DictionaryPopup',
  
  props: {
    elements: {
      type: Array,
      required: true
    }
  }
}
</script>

<style src="../styles/DictionaryPopup.css" scoped></style>