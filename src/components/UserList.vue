<script setup lang="ts">
import type {User} from "@/models/user";
import {computed, ref, watch} from "vue";
import VueInput from "@/components/VueInput.vue";
import SearchBar from "@/components/SearchBar.vue";
import VueModal from "@/components/VueModal.vue";

const props = defineProps<{
  users: User[]
}>();

const refUsers = ref<User[]>([]);
const userToUpdate = ref<User>({
  email: '',
  phoneNumber: '',
  dateOfBirth: null,
  name: ''
});

watch(
    () => props.users,
    (newUsers) => {
      refUsers.value = newUsers;
    },
    {immediate: true}
);

const emit = defineEmits({
  deleteUser: (user: User) => {
    return user !== null;
  },
  updateUser: (user: User) => {
    return user && user.name.length > 0
        && user.dateOfBirth && new Date(user.dateOfBirth) < new Date()
        && /^\+?3?8?(0\d{9})$/.test(user.phoneNumber)
        && /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(user.email)
  }
});

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
  query = query.trim();
  console.log(query);
  if (!query) {
    refUsers.value = props.users;
  } else {
    refUsers.value = refUsers.value.filter(u => u.name.toLowerCase().includes(query.toLowerCase()));
  }
}

const isUpdateModalOpen = ref(false);
const isDeleteModalOpen = ref(false);

const openUpdateModal = () => (isUpdateModalOpen.value = true);
const closeUpdateModal = () => (isUpdateModalOpen.value = false);

const openDeleteModal = () => (isDeleteModalOpen.value = true);
const closeDeleteModal = () => (isDeleteModalOpen.value = false);

const handleSubmit = () => {
  emit('updateUser', {...userToUpdate.value!});
  formSubmitted.value = false;
  clearValues();
  clearTouchedFields();
  closeUpdateModal();
};

const touchedFields = ref({
  email: false,
  dateOfBirth: false,
  name: false,
  phoneNumber: false
});


const formSubmitted = ref(false);

const emailIsValid = computed(() => {
  if (userToUpdate.value!.email.length === 0 && !touchedFields.value.email) {
    return null;
  }
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(userToUpdate.value!.email);
});

const phoneNumberIsValid = computed(() => {
  if (userToUpdate.value!.phoneNumber.length === 0 && !touchedFields.value.phoneNumber) {
    return null;
  }
  const regex = /^\+?3?8?(0\d{9})$/;
  return regex.test(userToUpdate.value!.phoneNumber);
})

const usernameIsValid = computed(() => {
  if (userToUpdate.value!.name.length === 0 && !touchedFields.value.name) {
    return null;
  }
  return !!userToUpdate.value!.name;
});

const dateOfBirthIsValid = computed(() => {
  if (!userToUpdate.value!.dateOfBirth && !touchedFields.value.dateOfBirth) {
    return null;
  }
  return userToUpdate.value!.dateOfBirth && new Date(userToUpdate.value!.dateOfBirth) < new Date();
});

function clearValues() {
  userToUpdate.value!.dateOfBirth = null;
  userToUpdate.value!.name = '';
  userToUpdate.value!.phoneNumber = '';
  userToUpdate.value!.email = '';
}

function clearTouchedFields() {
  touchedFields.value.phoneNumber = false;
  touchedFields.value.email = false;
  touchedFields.value.name = false;
  touchedFields.value.dateOfBirth = false;
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
            <button @click="() => {userToDelete = user; openDeleteModal();}" type="button" class="btn btn-danger" data-bs-toggle="modal">
              Delete
            </button>
            <button @click="() => {userToUpdate = user; openUpdateModal();}" type="button" class="btn btn-success" data-bs-toggle="modal">
              Update
            </button>
          </div>
        </td>
      </tr>
      </tbody>
    </table>

    <VueModal id="deleteUser" :isOpen="isDeleteModalOpen" @close="closeDeleteModal">
      <template #header>
        <h5 class="modal-title" id="exampleModalLabel">Delete User</h5>
      </template>
      <template #body>
        <div>
          Are you sure you want to delete user: {{ userToDelete?.name }}, {{ userToDelete?.email }}
        </div>
      </template>
      <template #footer>
        <button type="button" @click="() => {$emit('deleteUser', userToDelete); closeDeleteModal();}"
                class="btn btn-primary">Yes
        </button>
      </template>
    </VueModal>

    <VueModal id="updateModal" :isOpen="isUpdateModalOpen" @close="closeUpdateModal">
      <template #header><h5 class="modal-title">Update User</h5></template>
      <template #body>
        <form @submit.prevent="handleSubmit">
          <div class="mb-3">
            <label for="name" class="form-label">Name</label>
            <VueInput id="name"
                      placeholder="Enter user name"
                      v-model="userToUpdate!.name"
                      :is-valid="usernameIsValid"></VueInput>
            <div class="invalid-feedback">
              Username is required.
            </div>
          </div>

          <div class="mb-3">
            <label for="dob" class="form-label">Date of Birth</label>
            <VueInput id="dob"
                      v-model="userToUpdate!.dateOfBirth"
                      placeholder="Enter date of birth"
                      type="date"
                      :is-valid="dateOfBirthIsValid"></VueInput>
            <div class="invalid-feedback">
              Date of Birth is required and should be less than present.
            </div>
          </div>

          <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <VueInput :disabled="true"
                      id="email"
                      placeholder="Enter email"
                      :is-valid="emailIsValid"
                      v-model="userToUpdate!.email"></VueInput>
            <div class="invalid-feedback">
              Email is required and should be in valid format.
            </div>
          </div>

          <div class="mb-3">
            <label for="phoneNumber" class="form-label">Phone Number</label>
            <VueInput id="phoneNumber"
                      v-model="userToUpdate!.phoneNumber"
                      pattern="^\+?3?8?(0\d{9})$"
                      placeholder="Enter phone number"
                      :is-valid="phoneNumberIsValid"></VueInput>
            <div class="invalid-feedback">
              Phone Number is required and should be in valid format for Ukraine.
            </div>
          </div>
        </form>
      </template>
      <template #footer>
        <button type="submit" class="btn btn-primary" @click="() => {handleSubmit(); closeUpdateModal();}">Submit</button>
      </template>
    </VueModal>
  </div>
</template>

<style scoped>

</style>