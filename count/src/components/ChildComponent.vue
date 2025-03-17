<template>
  <div>
    <p>Текущий счетчик: {{ counter }}</p>
  </div>
</template>

<script>
export default {
  props: {
    counter: Number
  }
};
</script>