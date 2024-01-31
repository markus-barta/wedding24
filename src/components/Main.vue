<template>
  <v-container>
    <v-form @submit.prevent="handleSubmit">

      <v-card
        class="mx-auto person-card"
        width="50%"
        prepend-icon="mdi-account"
      >
        <template v-slot:title>
          {{ form.name }}
        </template>

        <v-card-text>
          {{ form.uuid }}
        </v-card-text>
      </v-card>

      <div>
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
        </div>
    </v-form>

    <v-alert v-if="showSuccessMessage" type="success" dismissible class="success-message">
      Form successfully submitted!
    </v-alert>
  </v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import { v4 as uuidv4 } from 'uuid'; // Ensure you have uuid installed or import it

const form = ref({
  name: '',
  email: '',
  uuid: '' 
});

const deploymentId = 'AKfycbyUdkCjI2xH6JcSJs-dFRKeuAKQ0hW0snuwCvgWVPb1tVkQFsFtsQXaSORmJWuC-KvV';

const showSuccessMessage = ref(false);

const getOrGenerateUUID = () => {
  let uuid = localStorage.getItem('userUUID');
  if (!uuid) {
    uuid = uuidv4();
    localStorage.setItem('userUUID', uuid);
  }
  form.value.uuid = uuid; // Store UUID in form data
};

const saveFormData = () => {
  // Save only name and email to avoid overwriting UUID with empty string
  const { uuid, ...dataToSave } = form.value;
  localStorage.setItem('formData', JSON.stringify(dataToSave));
};

const loadFormData = () => {
  const savedData = localStorage.getItem('formData');
  if (savedData) {
    Object.assign(form.value, JSON.parse(savedData));
  }
  getOrGenerateUUID(); // Ensure UUID is loaded or generated
};

onMounted(() => {
  loadFormData();
});

const handleSubmit = async () => {
  saveFormData();

  // Ensure UUID is included in form data for submission
  const formData = new URLSearchParams(form.value).toString();

  try {
    const response = await fetch('https://script.google.com/macros/s/' + deploymentId + '/exec', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: formData,
    });

    if (!response.ok) throw new Error('Network response was not ok.');

    showSuccessMessage.value = true;
    setTimeout(() => {
      showSuccessMessage.value = false;
    }, 5000); // Hide success message after 5 seconds
  } catch (error) {
    console.error('Error:', error);
  }
};
</script>

<style>
  .person-card {margin: 40px 0;}
  .success-message {margin: 40px 0;}
</style>