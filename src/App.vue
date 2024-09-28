<script setup lang="ts">
import WinnersList from "@/components/WinnersList.vue";
import UserForm from "@/components/UserForm.vue";
import UserList from "@/components/UserList.vue";
import {ref} from "vue";
import type {User} from "@/models/user";

const users = ref<User[]>([
  {
    email: "ivan.petrenko@gmail.com",
    name: "Ivan Petrenko",
    phoneNumber: "+380951234567",
    dateOfBirth: new Date(1995, 4, 23)
  },
  {
    email: "olena.kovalenko@gmail.com",
    name: "Olena Kovalenko",
    phoneNumber: "+380971234567",
    dateOfBirth: new Date(2000, 1, 15)
  },
  {
    email: "serhiy.shevchenko@gmail.com",
    name: "Serhiy Shevchenko",
    phoneNumber: "+380931234567",
    dateOfBirth: new Date(1988, 3, 7)
  },
  {
    email: "tatiana.sidorova@gmail.com",
    name: "Tatiana Sidorova",
    phoneNumber: "+380981234567",
    dateOfBirth: new Date(1992, 8, 19)
  },
  {
    email: "oleg.ivanov@gmail.com",
    name: "Oleg Ivanov",
    phoneNumber: "+380955555555",
    dateOfBirth: new Date(1985, 11, 31)
  }
]);
const winners = ref<User[]>([]);
const addUser = (user: User) => {
  console.log(user);
  users.value.push(user);
}

const deleteWinner = (user: User) => {
  console.log(user);
  winners.value = winners.value.filter(x => x.email !== user.email);
}

const addNewWinner = () => {
  if (winners.value.length > 3) {
    return;
  }

  const randomIndex = Math.floor(Math.random() * users.value.length);
  const winner = users.value[randomIndex];
  if (winners.value.filter(w => w.email === winner.email).length > 0) {
    return;
  }
  console.log(winner);
  winners.value.push(winner);
}
</script>

<template>
  <div class="w-75 m-auto">
    <WinnersList :users-count="users.length" @deleteWinner="deleteWinner" :winners="winners"
                 @newWinner="addNewWinner"></WinnersList>
    <UserForm @addUser="addUser"></UserForm>
    <UserList :users="users"></UserList>
  </div>
</template>

<style scoped>

</style>