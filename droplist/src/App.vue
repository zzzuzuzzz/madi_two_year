<template>
  <div class="card">
    <h1>Выбор элементов</h1>
    <input id="inp" type="file" @change="handleFileChange" accept=".json, .xml"/>

    <button @click="openPopup1" :disabled="!elements.length">Открыть Попап 1</button>
    <MyPopup
        v-if="showPopup1"
        :elements="elements"
        v-model="popup1Value"
        @close="closePopup1"
    />
    <p v-if="popup1Value.text">Попап 1: {{ selectedElementTexts1.join(', ') }}</p>

    <button @click="openPopup2" :disabled="!elements.length">Открыть Попап 2</button>
    <MyPopup
        v-if="showPopup2"
        :elements="elements"
        v-model="popup2Value"
        @close="closePopup2"
    />
    <p v-if="popup2Value.text">Попап 2: {{ selectedElementTexts2.join(', ') }}</p>

    <h2 v-if="intersection.length">Пересечение:</h2>
    <p v-if="intersection.length">{{ intersectionTexts.join(', ') }}</p>
    <button @click="clear" :disabled="!elements.length">Отчистить</button>

  </div>
</template>

<script>
import MyPopup from './components/MyPopup.vue';

export default {
  components: {
    MyPopup
  },
  
  data() {
    return {
      elements: [],
      showPopup1: false,
      showPopup2: false,
      popup1Value: { text: '', selected: [] },
      popup2Value: { text: '', selected: [] }
    };
  },
  
  computed: {
    selectedElementTexts1() {
      return this.popup1Value.selected.map(id => {
        const element = this.elements.find(el => el.id === id);
        return element ? element.name : '';
      });
    },
    
    selectedElementTexts2() {
      return this.popup2Value.selected.map(id => {
        const element = this.elements.find(el => el.id === id);
        return element ? element.name : '';
      });
    },
    
    intersection() {
      return this.popup1Value.selected.filter(id => this.popup2Value.selected.includes(id));
    },
    
    intersectionTexts() {
      return this.intersection.map(id => {
        const element = this.elements.find(el => el.id === id);
        return element ? element.name : '';
      });
    }
  },
  
  methods: {
    openPopup1() {
      this.showPopup1 = true;
    },
    
    closePopup1() {
      this.showPopup1 = false;
    },
    
    openPopup2() {
      this.showPopup2 = true;
    },
    
    closePopup2() {
      this.showPopup2 = false;
    },
    
    handleFileChange(event) {
      const file = event.target.files[0];
      if (file) {
        this.popup1Value = { text: '', selected: [] };
        this.popup2Value = { text: '', selected: [] };
        this.elements = []; // Сбрасываем elements до разбора нового файла

        const reader = new FileReader();
        reader.addEventListener('load', (e) => this.fileReaderOnload(file, e));
        reader.readAsText(file);
      }
    },
    
    fileReaderOnload(file, e) {
      const fileContent = e.target.result;
      this.parseFile(file, fileContent);
    },
    
    parseFile(file, fileContent) {
      try {
        if (file.name.endsWith('.json')) {
          this.elements = JSON.parse(fileContent);
        } else if (file.name.endsWith('.xml')) {
          this.elements = this.parseXml(fileContent);
        } else {
          console.error('Неподдерживаемый формат файла');
          this.elements = [];
          return;
        }
      } catch (error) {
        console.error('Ошибка при разборе файла:', error);
        this.elements = [];
      }
    },
    
    parseXml(xmlString) {
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
    },
    
    clear() {
      this.popup1Value = { text: '', selected: [] };
      this.popup2Value = { text: '', selected: [] };
      this.elements = [];
      document.getElementById('inp').value = null;
    }
  }
};
</script>

<style src="./styles/App.css" scoped></style>