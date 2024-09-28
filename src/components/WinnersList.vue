<script setup lang="ts">
import type {User} from "@/models/user";

defineProps<{
  winners: User[],
  usersCount: number
}>();

const emit = defineEmits(['new-winner', 'delete-winner']);

const deleteWinner = (winner: User) => {
  emit('delete-winner', {...winner});
}
</script>

<template>
<div class="mb-3 mt-5 p-4 d-flex bg-white border border-1 border-secondary rounded">
  <div class="w-75 border border-secondary border-1 p-3 rounded">
    <div class="d-flex">
      <span class="rounded text-white bg-primary p-1 ms-1 me-1" v-for="(winner, index) of winners" :key="index">
        {{winner.name}}
        <i role="button" @click="() => deleteWinner(winner)" class="ps-2 fa-solid fa-xs text-black">x</i>
      </span>
      <span class="text-secondary p-1">Winners</span>
    </div>
  </div>
  <div class="w-25 d-flex justify-content-center align-items-center">
    <button :disabled="winners.length === 3 || usersCount === 0" class="btn btn-primary" @click="$emit('new-winner')">New Winner</button>
  </div>
</div>
</template>

<style scoped>

</style>