<script setup lang="ts">
import {computed, ref} from "vue";
import type {User} from "@/models/user";
import VueButton from "@/components/VueButton.vue";
import VueInput from "@/components/VueInput.vue";

const touchedFields = ref({
  email: false,
  dateOfBirth: false,
  name: false,
  phoneNumber: false
});

const user = ref<User>({
  email: '',
  dateOfBirth: null,
  name: '',
  phoneNumber: ''
});

const emit = defineEmits({
  addUser: (user: User) => {
    return user && user.name.length > 0
        && user.dateOfBirth && new Date(user.dateOfBirth) < new Date()
        && /^\+?3?8?(0\d{9})$/.test(user.phoneNumber)
        && /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(user.email)
  }
});

const formSubmitted = ref(false);

const emailIsValid = computed(() => {
  if (user.value.email.length === 0 && !touchedFields.value.email) {
    return null;
  }
  const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  return regex.test(user.value.email);
});

const phoneNumberIsValid = computed(() => {
  if (user.value.phoneNumber.length === 0 && !touchedFields.value.phoneNumber) {
    return null;
  }
  const regex = /^\+?3?8?(0\d{9})$/;
  return regex.test(user.value.phoneNumber);
})

const usernameIsValid = computed(() => {
  if (user.value.name.length === 0 && !touchedFields.value.name) {
    return null;
  }
  return !!user.value.name;
});

const dateOfBirthIsValid = computed(() => {
  if (!user.value.dateOfBirth && !touchedFields.value.dateOfBirth) {
    return null;
  }
  return user.value.dateOfBirth && new Date(user.value.dateOfBirth) < new Date();
});

function clearValues() {
  user.value.dateOfBirth = null;
  user.value.name = '';
  user.value.phoneNumber = '';
  user.value.email = '';
}

function clearTouchedFields() {
  touchedFields.value.phoneNumber = false;
  touchedFields.value.email = false;
  touchedFields.value.name = false;
  touchedFields.value.dateOfBirth = false;
}

const onSubmit = () => {
  emit('addUser', {...user.value});
  formSubmitted.value = false;
  clearValues();
  clearTouchedFields();
};
</script>


<template>
  <div class="pe-5 ps-5 pb-3 pt-3 rounded bg-white border border-1 border-secondary">
    <form @submit.prevent="onSubmit" class="needs-validation" novalidate>
      <h1>Register form</h1>
      <p class="text-grey">Please fill in all the fields</p>
      <div class="mb-3">
        <label for="name" class="form-label">Name</label>
        <VueInput :is-valid="usernameIsValid"
                  id="name"
                  placeholder="Enter user name"
                  v-model="user.name"
                  @blur="touchedFields.name = true"></VueInput>
        <div class="invalid-feedback">
          Username is required.
        </div>
      </div>

      <div class="mb-3">
        <label for="dob" class="form-label">Date of Birth</label>
        <VueInput id="dob"
                  :is-valid="dateOfBirthIsValid"
                  v-model="user.dateOfBirth"
                  placeholder="Enter date of birth"
                  type="date"
                  @blur="touchedFields.dateOfBirth = true"></VueInput>
        <div class="invalid-feedback">
          Date of Birth is required and should be less than present.
        </div>
      </div>

      <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <VueInput id="email"
                  :is-valid="emailIsValid"
                  placeholder="Enter email"
                  v-model="user.email"
                  @blur="touchedFields.email = true"></VueInput>
        <div class="invalid-feedback">
          Email is required and should be in valid format.
        </div>
      </div>

      <div class="mb-3">
        <label for="phoneNumber" class="form-label">Phone Number</label>
        <VueInput id="phoneNumber"
                  :is-valid="phoneNumberIsValid"
                  v-model="user.phoneNumber"
                  pattern="^\+?3?8?(0\d{9})$"
                  placeholder="Enter phone number"
                  @blur="touchedFields.phoneNumber = true"></VueInput>
        <div class="invalid-feedback">
          Phone Number is required and should be in valid format for Ukraine.
        </div>
      </div>

      <div class="d-flex justify-content-end">
        <VueButton button-type="submit" button-style="primary">Submit</VueButton>
      </div>
    </form>
  </div>
</template>

<style scoped>
.text-grey {
  color: grey;
}
</style>