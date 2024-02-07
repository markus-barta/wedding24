<template>
  <v-container>
    <v-form ref="formModel" @submit.prevent="handleSubmit">

      <v-card
        class="mx-auto person-card"
        width="50%"
        prepend-icon="mdi-account"
      >
        <template v-slot:title>
          {{ form.guestName }}
        </template>

        <v-card-text>
          {{ form.uuid }}
        </v-card-text>
      </v-card>

      <div class="form">
        <v-sheet class="sheet"
          :elevation="5"
          border
          rounded
        >
          <v-text-field
            v-model="form.email"
            label="Email"
            type="email"
            :rules="[v => !!v || 'Bitte gib eine EMailadresse an!']"
          ></v-text-field>

          <v-select
            v-model="form.rsvp"
            label="Antwort"
            auto-select-first
            :items="['Ich komme gern', 'Ich komme leider nicht']"
          ></v-select>

          <v-select
            v-model="form.bookRoom"
            label="Zimmer"
            auto-select-first
            :items="['1x Einzelzimmer', '1x Doppelzimmer', '1x Doppelzimmer und 1x Einzelzimmer', '2x Einzelzimmer', 'Keines']"
            v-if="form.rsvp === 'Ich komme gern'"
          ></v-select>
        </v-sheet>

        <v-sheet
          :elevation="5"
          border
          rounded
          class="sheet"
          v-if="form.rsvp === 'Ich komme gern'"
        >

        <v-text-field
            v-model="form.guestName"
            label="Vorname Nachname"
          ></v-text-field>

          <v-text-field
            v-model="form.companionName"
            label="Begleitung Vorname Nachname"
          ></v-text-field>

          <v-text-field
            v-model="form.kidsNumber"
            label="Komme in Begleitung von Kindern"
            type="number"
            min="0"
          ></v-text-field>

          <v-select
            v-model="form.foodPreference"
            label="Abendessen"
            auto-select-first
            :items="['Fleisch', 'Fisch', 'Vegetarisch', 'Vegan', 'Individuell/Allergie', 'Keines']"
          ></v-select>

          <v-text-field
            v-if="form.foodPreference === 'Individuell/Allergie'"
            v-model="form.customFoodPreference"
            label="Individuelle Essenswünsche und/oder Allergien"
            persistent-hint
          ></v-text-field>

        </v-sheet>

        <v-btn
          type="submit" 
          :disabled="submitDisabled"
          color="primary"
          :loading="submitInProgress" 
        >
          Absenden
        </v-btn>
        </div>
    </v-form>

    <v-alert v-if="showSuccessMessage" type="success" dismissible class="alert-message">
      Danke für die Rückmeldung, {{ form.guestName }}!
    </v-alert>

    <v-alert v-if="showValidationFailMessage" type="error" dismissible class="alert-message">
      Bitte die Pflichtfelder ausfüllen!
    </v-alert>
  </v-container>
</template>

<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
import { v4 as uuidv4 } from 'uuid'; // Ensure you have uuid installed or import it

const form = reactive({
  email: '',
  rsvp: 'Ich komme gern',
  bookRoom: '',
  guestName: '',
  companionName: '',
  kidsNumber: 0,
  foodPreference: '',
  customFoodPreference: '',
  uuid: '' 
});

const deploymentId = 'AKfycbyUdkCjI2xH6JcSJs-dFRKeuAKQ0hW0snuwCvgWVPb1tVkQFsFtsQXaSORmJWuC-KvV';
const submitDisabled = ref(false);
const showSuccessMessage = ref(false);
const showValidationFailMessage = ref(false);
const submitInProgress = ref(false);
const formModel = ref(null);

const enableSubmitButton = () => {
  submitDisabled.value = false;
}

const getOrGenerateUUID = () => {
  let uuid = localStorage.getItem('userUUID');
  if (!uuid) {
    uuid = uuidv4();
    localStorage.setItem('userUUID', uuid);
  }
  form.uuid = uuid; // Store UUID in form data
};

const saveFormData = () => {
  // Save only name and email to avoid overwriting UUID with empty string
  const { uuid, ...dataToSave } = form;
  localStorage.setItem('formData', JSON.stringify(dataToSave));
};

const loadFormData = () => {
  const savedData = localStorage.getItem('formData');
  if (savedData) {
    Object.assign(form, JSON.parse(savedData));
  }
  getOrGenerateUUID(); // Ensure UUID is loaded or generated
};

onMounted(() => {
  loadFormData();
});

const handleSubmit = async () => {
  showValidationFailMessage.value = false;
  saveFormData();

  const formValidation = await formModel.value.validate();
  if (!formValidation.valid) {
    showValidationFailMessage.value = true;
    setTimeout(() => {
      showValidationFailMessage.value = false;
    }, 5000); // Hide message after 5 seconds
    return;
  }

  submitDisabled.value = true;
  submitInProgress.value = true;

  // Ensure UUID is included in form data for submission
  const formData = new URLSearchParams(form).toString();

  try {
    const response = await fetch('https://script.google.com/macros/s/' + deploymentId + '/exec', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: formData,
    });

    submitInProgress.value = false;
    if (!response.ok) throw new Error('Network response was not ok.');

    showSuccessMessage.value = true;
    setTimeout(() => {
      showSuccessMessage.value = false;
    }, 5000); // Hide success message after 5 seconds
  } catch (error) {
    console.error('Error:', error);
  }
};

watch(form, async () => {
  enableSubmitButton();
})
</script>

<style>
  .person-card {margin: 40px 0;}
  .alert-message {margin: 40px 0;}
  .form {
    display: flex;
    gap: 15px;
    flex-direction: column;
  }
  .form .sheet {
    padding: 15px;
  }
</style>