<script setup>
const props = defineProps({
  modelValue: { type: String, default: "" },
});
const emits = defineEmits(["submit", "update:modelValue"]);
</script>
<template>
  <div class="input-group">
    <input
      type="text"
      class="form-control"
      placeholder="Nhập thông tin cần tìm"
      :value="modelValue"
      @input="emits('update:modelValue', $event.target.value)"
      @keyup.enter="emits('submit')"
    />

    <div class="input-group-append">
      <button
        class="btn btn-outline-secondary"
        type="button"
        @click="emits('submit')"
      >
        <i class="fas fa-search"></i> Tìm kiếm
      </button>
    </div>
  </div>
</template>
