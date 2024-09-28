<script setup lang="ts">
import type {User} from "@/models/user";

defineProps<{
  users: User[]
}>();
</script>

<template>
  <div class="mt-3 p-4 mb-5 border border-1 border-secondary rounded bg-white">
    <table class="table rounded">
      <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
        <th scope="col">Email</th>
        <th scope="col">Date Of Birth</th>
        <th scope="col">Phone Number</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(user, index) of users" :key="index">
        <td>{{ index + 1 }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user.email }}</td>
        <td>{{ user.dateOfBirth }}</td>
        <td>{{ user.phoneNumber }}</td>
      </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>

</style>