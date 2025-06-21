<template>
  <div class="popup">
    <div class="popup-content">
      <h2>Выберите элементы</h2>

      <input
          type="text"
          v-model="inputText"
          placeholder="Введите или выберите элементы"
          @input="handleInput"
          @paste="handlePaste"
      />

      <button @click="showDictionary = true">Словарь</button>

      <ul>
        <li
            v-for="element in elements"
            :key="element.id"
            @click="toggleSelection(element.id)"
            :class="{ selected: selectedElements.includes(element.id) }"
        >
          {{ element.name }} ({{ element.id }})
        </li>
      </ul>

      <div class="buttonGroup">
        <button @click="$emit('close')">Закрыть</button>
        <button @click="clear">Отчистить</button>
      </div>

      <DictionaryPopup 
        v-if="showDictionary" 
        :elements="elements" 
        @close="showDictionary = false"
      />
    </div>
  </div>
</template>

<script>
import DictionaryPopup from './DictionaryPopup.vue';

export default {
  name: 'MyPopup',
  components: { DictionaryPopup },
  
  props: {
    elements: { type: Array, required: true },
    modelValue: { 
      type: Object,
      default: () => ({ text: '', selected: [] })
    }
  },
  
  data() {
    return {
      inputText: this.modelValue.text,
      selectedElements: this.modelValue.selected,
      previousValidText: this.modelValue.text,
      showDictionary: false
    };
  },
  
  watch: {
    selectedElements: {
      handler(newSelected) {
        this.$emit('update:modelValue', { 
          text: this.inputText, 
          selected: newSelected 
        });
      }
    }
  },
  
  methods: {
    clear() {
      this.selectedElements = [];
      this.inputText = '';
    },

    handleInput(event) {
      const newText = event.target.value.toUpperCase();
      const validIds = this.elements.map(el => el.id);
      
      // Проверка валидности ввода
      const uniqueChars = new Set(newText);
      const isValid = Array.from(uniqueChars).every(char => validIds.includes(char)) && uniqueChars.size === newText.length;
      
      if (isValid) {
        this.previousValidText = newText;
        this.inputText = newText;
        this.selectedElements = Array.from(uniqueChars);
        this.emitUpdate();
      } else {
        this.inputText = this.previousValidText;
      }
    },
    
    handlePaste(event) {
      const pastedText = event.clipboardData.getData('text');
      
      if (!pastedText) return;
      
      const validIds = this.elements.map(el => el.id);
      const currentChars = new Set(this.previousValidText);
      const validNewChars = [];
      
      // Фильтрация нового текста
      for (const char of pastedText.toUpperCase()) {
        if (validIds.includes(char) && !currentChars.has(char) && !validNewChars.includes(char)) {
          validNewChars.push(char);
          currentChars.add(char);
        }
      }
      
      if (validNewChars.length) {
        this.inputText = this.previousValidText + validNewChars.join('');
        this.previousValidText = this.inputText;
        this.selectedElements = Array.from(currentChars);
        this.emitUpdate();
      }
    },
    
    toggleSelection(id) {
      const index = this.selectedElements.indexOf(id);
      
      if (index >= 0) {
        this.selectedElements.splice(index, 1);
      } else {
        this.selectedElements.push(id);
      }
      
      this.inputText = this.selectedElements.join('');
      this.previousValidText = this.inputText;
      this.emitUpdate();
    },
    
    emitUpdate() {
      this.$emit('update:modelValue', { 
        text: this.inputText, 
        selected: this.selectedElements 
      });
    }
  }
};
</script>

<style src="../styles/MyPopup.css" scoped></style>