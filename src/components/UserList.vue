<script setup lang="ts">
import type {User} from "@/models/user";
import VueButton from "@/components/VueButton.vue";
import {computed, ref, watch} from "vue";
import VueInput from "@/components/VueInput.vue";
import UpdateUserForm from "@/components/UpdateUserForm.vue";
import SearchBar from "@/components/SearchBar.vue";

const props = defineProps<{
  users: User[]
}>();

const refUsers = ref<User[]>([]);
const userToUpdate = ref<User | null>(null);

watch(
    () => props.users,
    (newUsers) => {
      refUsers.value = newUsers;
    },
    {immediate: true}
);

const emit = defineEmits(['delete-user', 'update-user']);
const updateUser = (user: User) => {
  console.log("Accepted");
  emit('update-user', user);
}
const userToDelete = ref<User>();

const sortedByNameAsc = ref(false);
const sortedByEmailAsc = ref(false);

const handleSortByName = () => {
  sortedByNameAsc.value = !sortedByNameAsc.value;
  if (sortedByNameAsc.value) {
    refUsers.value = refUsers.value.sort((a, b) => a.name.localeCompare(b.name))
  } else {
    refUsers.value = refUsers.value.sort((a, b) => b.name.localeCompare(a.name))
  }
}

const handleSortByEmail = () => {
  sortedByEmailAsc.value = !sortedByEmailAsc.value;
  if (sortedByEmailAsc.value) {
    refUsers.value = refUsers.value.sort((a, b) => a.email.localeCompare(b.email))
  } else {
    refUsers.value = refUsers.value.sort((a, b) => b.email.localeCompare(a.email))
  }
}

const handleSearch = (query: string) => {
  console.log(query);
  if(!query){
    refUsers.value = props.users;
  } else {
    refUsers.value = refUsers.value.filter(u => u.name.toLowerCase().includes(query.toLowerCase()));
  }
}
</script>

<template>
  <div class="mt-5 d-flex gap-1">
    <button @click="handleSortByName" class="btn btn-primary">Sort by Name <i class="bi"
                                                                              :class="sortedByNameAsc ? 'bi-sort-alpha-down' : 'bi-sort-alpha-up'"></i>
    </button>
    <button @click="handleSortByEmail" class="btn btn-primary">Sort by Email <i class="bi"
                                                                                :class="sortedByEmailAsc ? 'bi-sort-alpha-down' : 'bi-sort-alpha-up'"></i>
    </button>
    <SearchBar @search="handleSearch"></SearchBar>
  </div>
  <div class="mt-3 p-4 mb-5 border border-1 border-secondary rounded bg-white">
    <table class="table rounded">
      <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">Name</th>
        <th scope="col">Email</th>
        <th scope="col">Date Of Birth</th>
        <th scope="col">Phone Number</th>
        <th scope="col">Options</th>
      </tr>
      </thead>
      <tbody>
      <tr v-for="(user, index) of refUsers" :key="index">
        <td>{{ index + 1 }}</td>
        <td>{{ user.name }}</td>
        <td>{{ user.email }}</td>
        <td>{{ user.dateOfBirth }}</td>
        <td>{{ user.phoneNumber }}</td>
        <td>
          <div class="d-flex gap-1">
            <button @click="userToDelete = user" type="button" class="btn btn-danger" data-bs-toggle="modal"
                    data-bs-target="#deleteUserModal">
              Delete
            </button>
            <button @click="userToUpdate = user" type="button" class="btn btn-success" data-bs-toggle="modal"
                    data-bs-target="#updateUserModal">
              Update
            </button>
          </div>
        </td>
      </tr>
      </tbody>
    </table>
    <div class="modal fade" id="deleteUserModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Delete User</h1>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
          </div>
          <div class="modal-body">
            Are you sure you want to delete user: {{ userToDelete?.name }}, {{ userToDelete?.email }}
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">No</button>
            <button type="button" @click="$emit('delete-user', userToDelete)" data-bs-dismiss="modal" class="btn btn-primary">Yes</button>
          </div>
        </div>
      </div>
    </div>

    <UpdateUserForm @updateUser="updateUser" :user="userToUpdate"></UpdateUserForm>
  </div>
</template>

<style scoped>

</style>