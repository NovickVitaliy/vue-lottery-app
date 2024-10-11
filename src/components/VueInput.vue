<script setup lang="ts">
import type {InputType} from "@/models/input-type";

defineProps<{
  id: string;
  class: string;
  type: InputType;
  required?: boolean;
  placeholder: string;
  pattern?: string;
  disabled?: boolean
}>();

const model = defineModel();
</script>
<template>
  <input
      :id="id"
      v-model="model"
      :placeholder="placeholder"
      :required="required ?? true"
      :type="type"
      class="form-control"
      :class="class"
      :pattern="pattern ?? ''"
      :disabled="disabled ?? false">
</template>
