<template>
  <div class="popup">
    <div class="popup-content">
      <h2>Выберите элементы</h2>

      <input
          type="text"
          v-model="inputText"
          placeholder="Введите или выберите элементы"
          @paste.prevent
      />

      <button @click="showDictionary = true">
        <!-- Здесь можно использовать иконку, например, Font Awesome -->
        Словарь
      </button>

      <ul>
        <li
            v-for="element in elements"
            :key="element.id"
            @click="toggleSelection(element.id)"
            :class="{ selected: isSelected(element.id) }"
        >
          {{ element.name }} ({{ element.id }})
        </li>
      </ul>

      <button @click="closePopup">Закрыть</button>

      <DictionaryPopup v-if="showDictionary" :elements="elements" @close="showDictionary = false"/>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits, ref, computed, watch } from 'vue';
import DictionaryPopup from './DictionaryPopup.vue'; // Импортируйте компонент словаря

const props = defineProps({
  elements: {
    type: Array,
    required: true,
  },
  modelValue: {
    type: Object,
    default: () => ({ text: '', selected: [] })
  }
});

const emit = defineEmits(['update:modelValue', 'close']);

const inputText = ref(props.modelValue.text);
const selectedElements = ref(props.modelValue.selected);

// Валидация введенного текста
const validateInput = (text) => {
  const validIds = props.elements.map(el => el.id);
  for (const char of text) {
    if (!validIds.includes(char)) { // Валидируем уже в верхнем регистре
      return false; // Недопустимый символ
    }
  }
  return true;
};

watch(inputText, (newText) => {
  const upperCaseText = newText.toUpperCase(); // Приводим к верхнему регистру

  if (!validateInput(upperCaseText)) {
    inputText.value = inputText.value.slice(0, -1); // Удаляем последний символ из исходного ввода
    return;
  }

  selectedElements.value = upperCaseText.split('').filter(id => {
    return props.elements.some(el => el.id === id);
  });

  // Обновляем inputText, чтобы он всегда был в верхнем регистре
  inputText.value = upperCaseText;
});

watch(selectedElements, (newSelected) => {
  emit('update:modelValue', { text: inputText.value, selected: newSelected });
});

watch(inputText, (newText) => {
  emit('update:modelValue', { text: newText, selected: selectedElements.value });
});

const isSelected = (id) => {
  return selectedElements.value.includes(id);
};

const toggleSelection = (id) => {
  if (isSelected(id)) {
    selectedElements.value = selectedElements.value.filter((el) => el !== id);
  } else {
    selectedElements.value.push(id);
  }

  // Обновляем inputText при изменении выбранных элементов
  inputText.value = selectedElements.value.join('');
};

const showDictionary = ref(false);

const closePopup = () => {
  emit('close');
};
</script>

<style scoped>
.popup {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.popup-content {
  background-color: white;
  padding: 20px;
  border-radius: 5px;
  position: relative; /* Чтобы позиционировать кнопку относительно этого контейнера */
}

.popup-content button {
  margin-top: 10px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  padding: 5px;
  cursor: pointer;
}

li.selected {
  background-color: lightblue;
}
</style>