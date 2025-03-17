<template>
  <div>
    <button @click="incrementCounter">Увеличить счетчик</button>
    <ChildComponent :counter="counter" />
  </div>
</template>

<script>
import { ref } from 'vue';
import ChildComponent from './ChildComponent.vue';

export default {
  components: {
    ChildComponent
  },
  setup() {
    const counter = ref(0);

    const incrementCounter = () => {
      counter.value++;
    };

    return {
      counter,
      incrementCounter
    };
  }
};
</script>