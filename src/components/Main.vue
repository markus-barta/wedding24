<template>
    <v-container>
      <v-form @submit.prevent="handleSubmit">
        <v-text-field
          v-model="form.name"
          label="Name"
          required
        ></v-text-field>
  
        <v-text-field
          v-model="form.email"
          label="Email"
          type="email"
          required
        ></v-text-field>
  
        <v-btn type="submit" color="primary">Submit</v-btn>
      </v-form>
  
      <v-alert v-if="showSuccessMessage" type="success" dismissible>
        Form successfully submitted!
      </v-alert>
    </v-container>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  //import { VContainer, VForm, VTextField, VBtn, VAlert } from 'vuetify/lib';
  
  const form = ref({
    name: '',
    email: ''
  });
  
  const showSuccessMessage = ref(false);
  
  const saveFormData = () => {
    localStorage.setItem('formData', JSON.stringify(form.value));
  };
  
  const loadFormData = () => {
    const savedData = localStorage.getItem('formData');
    if (savedData) {
      Object.assign(form.value, JSON.parse(savedData));
    }
  };
  
  onMounted(() => {
    loadFormData();
  });
  
  const handleSubmit = () => {
    saveFormData();
  
    // Replace with your form submission logic
    console.log('Form submitted', form.value);
    showSuccessMessage.value = true;
  
    setTimeout(() => {
      showSuccessMessage.value = false;
    }, 5000); // Hide success message after 5 seconds
  };
  </script>
  