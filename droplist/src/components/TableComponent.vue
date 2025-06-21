<template>
  <table class="table table-striped">
    <thead>
    <tr>
      <th>ID</th>
      <th>Name</th>
      <th>Email</th>
    </tr>
    </thead>
    <tbody>
    <tr v-for="row in data" :key="row.id" @click="handleRowClick(row.id)" style="cursor: pointer;">
      <td>{{ row.id }}</td>
      <td>{{ row.name }}</td>
      <td>{{ row.email }}</td>
    </tr>
    </tbody>
  </table>
</template>

<script>

export default {
  props: {
    type: Array,
    required: true,
    data: Array
  },
  emits: ['row-clicked'],
  setup (props, { emit }) {
    const handleRowClick = (rowId) => {
      emit('row-clicked', rowId);
    };
    return {
      handleRowClick,
    };
  }
}
</script>

<style src="../styles/TableComponent.css" scoped></style>