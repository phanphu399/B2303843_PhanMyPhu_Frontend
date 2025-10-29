<script setup>
const props = defineProps({
  contacts: {
    type: Array,
    default: [],
  },
  activeIndex: { type: Number, default: -1 },
});

const emits = defineEmits(["update:activeIndex"]);
</script>
<template>
  <ul class="list-group">
    <li
      class="list-group-item list-group-item-action"
      v-for="(contact, index) in contacts"
      :key="contact._id"
      :class="{ active: index === activeIndex }"
      @click="emits('update:activeIndex', index)"
    >
      {{ contact.name }}
    </li>
  </ul>
</template>
<style scoped>
.list-group-item:hover {
  cursor: pointer;
}
</style>
