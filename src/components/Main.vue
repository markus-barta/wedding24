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

const handleSubmit = async () => {
  saveFormData();

  // Convert form data to URL-encoded string
  const formData = new URLSearchParams(form.value).toString();

  try {
    const response = await fetch('https://script.google.com/macros/s/AKfycbxndanosOtLJHYeV4R2ts4T_fcEJuML-_GHM2QAGI_32NqAcVE_NNvkCvMnSr6a_E9y/exec', {
      method: 'POST',
      // Removed mode: 'no-cors' to allow reading the response (ensure your Google Script publishes with proper CORS headers if needed)
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: formData,
    });

    // Check response status
    if (!response.ok) throw new Error('Network response was not ok.');

    // Process the response here, e.g., show success message
    showSuccessMessage.value = true;
    setTimeout(() => {
      showSuccessMessage.value = false;
    }, 5000); // Hide success message after 5 seconds
  } catch (error) {
    console.error('Error:', error);
    // Optionally, handle the error state (e.g., show an error message)
  }
};


</script>
