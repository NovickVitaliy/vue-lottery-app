<script setup lang="ts">
import WinnersList from '@/components/WinnersList.vue'
import UserForm from '@/components/UserForm.vue'
import UserList from '@/components/UserList.vue'
import {onMounted, ref, useTemplateRef} from 'vue'
import type { User } from '@/models/user'
import {LocalRepository} from "@/helpers/local-repository";

const users = ref<User[]>([]);
const winners = ref<User[]>([])
const usersKey: string = "vue-lottery-app.users";
const buttonRef = ref<HTMLButtonElement | null>(null);
onMounted(() => {
  users.value = LocalRepository.get(usersKey, []);
})

const updateUsersInStorage = () => {
  LocalRepository.set(usersKey, users.value);
};

const deleteUser = (user: User) => {
  users.value = users.value.filter(u => u !== user);
  updateUsersInStorage();
}

const addUser = (user: User) => {
  if(users.value.filter(u => u.email === user.email).length > 0){
    buttonRef.value.click();
    return;
  }
  users.value.push(user)
  updateUsersInStorage();
}

const deleteWinner = (user: User) => {
  console.log(user)
  winners.value = winners.value.filter((x) => x.email !== user.email)
}

const addNewWinner = () => {
  if (winners.value.length > 3) {
    return
  }

  const randomIndex = Math.floor(Math.random() * users.value.length)
  const winner = users.value[randomIndex]
  if (winners.value.filter((w) => w.email === winner.email).length > 0) {
    return
  }
  console.log(winner)
  winners.value.push(winner)
}

const updateUser = (user: User) => {
  console.log("In the start")
  console.log(users.value);
  users.value = users.value.filter(u => u.email !== user.email);
  users.value.push(user);
  console.log("Updated");
  console.log(users.value);
  updateUsersInStorage();
};

const some = ref<boolean>(true)
</script>

<template>
  <div class="w-75 m-auto">
    <WinnersList
      :users-count="users.length"
      @deleteWinner="deleteWinner"
      :winners="winners"
      @newWinner="addNewWinner"
    ></WinnersList>
    <UserForm @addUser="addUser"></UserForm>
    <UserList @updateUser="updateUser" @deleteUser="deleteUser" :users="users"></UserList>
  </div>

  <button ref="buttonRef" type="button" class="d-none" data-bs-toggle="modal" data-bs-target="#exampleModal">
  </button>

  <div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">Conflict while adding new user</h1>
          <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
          User with given email already exists
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary" data-bs-dismiss="modal">Ok</button>
        </div>
      </div>
    </div>
  </div>

</template>

<style scoped></style>
