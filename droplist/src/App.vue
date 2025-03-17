<template>
  <div>
    <h1>Выбор элементов</h1>
    <input type="file" @change="handleFileChange" accept=".json, .xml"/>

    <button @click="showPopup1 = true" :disabled="!elements.length">Открыть Попап 1</button>
    <my-popup
        v-if="showPopup1"
        :elements="elements"
        v-model="popup1Value"
        @close="showPopup1 = false"
    />
    <p v-if="popup1Value.text">Попап 1: {{ selectedElementTexts1.join(', ') }}</p>

    <button @click="showPopup2 = true" :disabled="!elements.length">Открыть Попап 2</button>
    <my-popup
        v-if="showPopup2"
        :elements="elements"
        v-model="popup2Value"
        @close="showPopup2 = false"
    />
    <p v-if="popup2Value.text">Попап 2: {{ selectedElementTexts2.join(', ') }}</p>

    <h2 v-if="intersection.length">Пересечение:</h2>
    <p v-if="intersection.length">{{ intersectionTexts.join(', ') }}</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import MyPopup from './components/MyPopup.vue';

const elements = ref([]);
const showPopup1 = ref(false);
const showPopup2 = ref(false);

// Используем ref для popup1Value и popup2Value, чтобы хранить данные для каждого попапа
const popup1Value = ref({ text: '', selected: [] });
const popup2Value = ref({ text: '', selected: [] });


const handleFileChange = (event) => {
  const file = event.target.files[0];
  if (file) {
    // Сбрасываем все значения
    popup1Value.value = { text: '', selected: [] };
    popup2Value.value = { text: '', selected: [] };
    elements.value = []; // Сбрасываем elements до разбора нового файла

    const reader = new FileReader();
    reader.onload = (e) => {
      const fileContent = e.target.result;
      parseFile(file, fileContent);
    };
    reader.readAsText(file);
  }
};


const parseFile = (file, fileContent) => {
  try {
    if (file.name.endsWith('.json')) {
      elements.value = JSON.parse(fileContent);
    } else if (file.name.endsWith('.xml')) {
      elements.value = parseXml(fileContent);
    } else {
      console.error('Неподдерживаемый формат файла');
      elements.value = [];
      return;
    }
  } catch (error) {
    console.error('Ошибка при разборе файла:', error);
    elements.value = [];
  }
};


function parseXml(xmlString) {
  const parser = new DOMParser();
  const xmlDoc = parser.parseFromString(xmlString, "text/xml");

  const items = xmlDoc.querySelectorAll('item');
  const result = [];

  items.forEach(item => {
    const id = item.getAttribute('id');  // Извлекаем id из атрибута
    const name = item.textContent;       // Извлекаем имя из содержимого элемента
    if (id && name) {
      result.push({ id, name });
    }
  });

  return result;
}


const selectedElementTexts1 = computed(() => {
  return popup1Value.value.selected.map(id => {
    const element = elements.value.find(el => el.id === id);
    return element ? element.name : '';
  });
});

const selectedElementTexts2 = computed(() => {
  return popup2Value.value.selected.map(id => {
    const element = elements.value.find(el => el.id === id);
    return element ? element.name : '';
  });
});

const intersection = computed(() => {
  return popup1Value.value.selected.filter(id => popup2Value.value.selected.includes(id));
});

const intersectionTexts = computed(() => {
  return intersection.value.map(id => {
    const element = elements.value.find(el => el.id === id);
    return element ? element.name : '';
  });
});

</script>

<style scoped>
/* Стилизуйте компоненты по своему вкусу */
</style>