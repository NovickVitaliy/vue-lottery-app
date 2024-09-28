<script setup lang="ts">
import {computed, ref, useTemplateRef, watch} from "vue";
import type {User} from "@/models/user";

const phoneNumberPattern = new RegExp("");

const user = ref<User>({
  email: '',
  dateOfBirth: null,
  name: '',
  phoneNumber: ''
});

const emit = defineEmits(['add-user']);
const formSubmitted = ref(false);

const emailIsValid = computed(() => {
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(user.value.email);
});

const phoneNumberIsValid = computed(() => {
  const regex = /^\+?3?8?(0\d{9})$/;
  return regex.test(user.value.phoneNumber);
})

const usernameIsValid = computed(() => {
  return user.value.name;
});

const dateOfBirthIsValid = computed(() => {
  return user.value.dateOfBirth && new Date(user.value.dateOfBirth) < new Date();
});
const onSubmit = () => {
  formSubmitted.value = true;
  let isValid = true;

  if(!emailIsValid.value){
    isValid = false;
  }

  if(!phoneNumberIsValid.value){
    isValid = false;
  }

  if(!usernameIsValid.value){
    isValid = false;
  }

  if(!dateOfBirthIsValid.value){
    isValid = false;
  }

  if(!isValid){
    return;
  }

  emit('add-user', {...user.value});
  formSubmitted.value = false;
  user.value.dateOfBirth = null;
  user.value.name = '';
  user.value.phoneNumber = '';
  user.value.email = '';
};
</script>


<template>
  <div class="pe-5 ps-5 pb-3 pt-3 rounded bg-white border border-1 border-secondary">
    <form @submit.prevent="onSubmit" class="needs-validation" novalidate>
      <h1>Register form</h1>
      <p class="text-grey">Please fill in all the fields</p>
      <div class="mb-3">
        <label for="name" class="form-label">Name</label>
        <input :class="{ 'is-invalid': formSubmitted && !usernameIsValid, 'is-valid': usernameIsValid}" id="name" class="form-control" type="text" required v-model="user.name" placeholder="Enter user name">
        <div class="invalid-feedback">
          Username is required.
        </div>
      </div>

      <div class="mb-3">
        <label for="dob" class="form-label">Date of Birth</label>
        <input :class="{ 'is-invalid': formSubmitted && !dateOfBirthIsValid, 'is-valid': dateOfBirthIsValid }" id="dob" class="form-control" type="date" required v-model="user.dateOfBirth"
               placeholder="Enter date of birth">
        <div class="invalid-feedback">
          Date of Birth is required and should be less than present.
        </div>
      </div>

      <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <input :class="{ 'is-invalid': formSubmitted && !emailIsValid, 'is-valid': emailIsValid }" pattern="" id="email" class="form-control" type="text" required v-model="user.email" placeholder="Enter email">
        <div class="invalid-feedback">
          Email is required and should be in valid format.
        </div>
      </div>

      <div class="mb-3">
        <label for="phoneNumber" class="form-label">Phone Number</label>
        <input :class="{ 'is-invalid': formSubmitted && !phoneNumberIsValid, 'is-valid': phoneNumberIsValid }" pattern="^\+?3?8?(0\d{9})$" id="phoneNumber" class="form-control" type="text" required v-model="user.phoneNumber"
               placeholder="Enter phone number">
        <div class="invalid-feedback">
          Phone Number is required and should be in valid format for Ukraine.
        </div>
      </div>

      <div class="d-flex justify-content-end">
        <button class="btn btn-primary" type="submit">Save</button>
      </div>
    </form>
  </div>
</template>

<style scoped>
.text-grey {
  color: grey;
}
</style>