<template>
  <div class="card mt-3">
    <div class="card-body">
      <h5 class="card-title">Details</h5>
      <p><strong>Name:</strong> {{ details.name }}</p>
      <p><strong>Email:</strong> {{ details.email }}</p>
      <p><strong>Age:</strong> {{ details.age }}</p>
      <p><strong>City:</strong> {{ details.city }}</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    details: {
      type: Object,
      required: true,
    },
  },
};
</script>

<style src="../styles/RowDetailsComponent.css" scoped></style>