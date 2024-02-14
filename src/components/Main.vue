<template>
  <v-container>
    <h1 class="centerHeader">Hochzeit von Mailina und Markus</h1>
    <v-form ref="formModel" @submit.prevent="handleSubmit">

      <div class="form">
        <v-card class="sheet"
          :elevation="5"
          border
          rounded
        >
        <!-- Basics -->
        <template v-slot:title>
          Grundlegendes
        </template>

        <v-text-field
            v-model="form.email"
            label="Kontakt-Email - an welche Adresse dürfen wir weitere Information, Photos, etc. senden."
            type="email"
            :rules="[v => !!v || 'Bitte gib eine EMailadresse an!']"
          ></v-text-field>

          <v-select
            v-model="form.rsvp"
            label="Antwort"
            auto-select-first
            :items="[cReplyStringPositive, cReplyStringNegative]"
          ></v-select>

          <v-text-field
            v-model="form.guestCount"
            label="Erwachsene"
            type="number"
            min="1"
            max="2"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-text-field>

          <v-text-field
            v-model="form.kidsCount"
            label="Kinder"
            type="number"
            min="0"
            max="9"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-text-field>

          <v-textarea
            v-model="form.kidsSpecialInfo"
            label="Anmerkungen, Essenswünsche oder Allergien der Kinder"
            persistent-hint
            auto-grow
            v-if="form.kidsCount > 0"
          ></v-textarea>

        </v-card>

        <!-- Guest 1 -->
        <v-card
          :elevation="5"
          border
          rounded
          class="sheet"
          prepend-icon="mdi-account"
          v-if="form.rsvp === cReplyStringPositive"
        >
        <template v-slot:title>
          Gast 1
        </template>

        <v-text-field
            v-model="form.guestName1"
            label="Vorname Nachname"
          ></v-text-field>

          <v-select
            v-model="form.bookRoom1"
            label="Zimmer"
            auto-select-first
            :items="cRoomOptions"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-select>

          <v-select
            v-model="form.foodPreference1"
            label="Abendessen"
            auto-select-first
            :items="['Fleisch', 'Fisch', 'Vegetarisch', 'Vegan', 'Individuell/Allergie', 'Keines']"
          ></v-select>

          <v-textarea
            v-if="form.foodPreference1 === 'Individuell/Allergie'"
            v-model="form.customFoodPreference1"
            label="Individuelle Essenswünsche und/oder Allergien"
            persistent-hint
            auto-grow
          ></v-textarea>

        </v-card>

        <!-- Guest 2 -->
        <v-card
          :elevation="5"
          border
          rounded
          class="sheet"
          prepend-icon="mdi-account"
          v-if="(form.rsvp === cReplyStringPositive) && (form.guestCount > 1)"
        >
        <template v-slot:title>
          Gast 2
        </template>

        <v-text-field
            v-model="form.guestName2"
            label="Vorname Nachname"
          ></v-text-field>

          <v-select
            v-model="form.bookRoom2"
            label="Zimmer"
            auto-select-first
            :items="cRoomOptions"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-select>

          <v-select
            v-model="form.foodPreference2"
            label="Abendessen"
            auto-select-first
            :items="['Fleisch', 'Fisch', 'Vegetarisch', 'Vegan', 'Individuell/Allergie', 'Keines']"
          ></v-select>

          <v-textarea
            v-if="form.foodPreference2 === 'Individuell/Allergie'"
            v-model="form.customFoodPreference2"
            label="Individuelle Essenswünsche und/oder Allergien"
            persistent-hint
            auto-grow
          ></v-textarea>

        </v-card>

        <v-card class="sheet"
          :elevation="5"
          border
          rounded
          v-if="form.rsvp === cReplyStringPositive"
        >
          <!-- Basics -->
          <template v-slot:title>
            Zusätzliche Informationen
          </template>

          <v-textarea
            v-model="form.additionalInfos"
            label="Sonstige Anmerkungen wie z.B. Liederwünsche"
            persistent-hint
            auto-grow
          ></v-textarea>

        </v-card>


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

    <!-- UUID -->
    <v-card
        class="mx-auto person-card"
        width="50%"
      >
        <v-card-text class="person-card-text">
          Antwort-ID: {{ form.uuid }}
        </v-card-text>
      </v-card>

    <v-alert v-if="showSuccessMessage" type="success" dismissible class="alert-message">
      Danke für die Rückmeldung, {{ form.guestName1 }}!
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
  rsvp: cReplyStringPositive,
  kidsCount: 0,
  kidsSpecialInfo: '',
  guestCount: 2,
  bookRoom1: '',
  guestName1: '',
  foodPreference1: '',
  customFoodPreference1: '',
  bookRoom2: '',
  guestName2: '',
  foodPreference2: '',
  customFoodPreference2: '',
  additionalInfos: '',
  uuid: '' 
});

//const deploymentId = 'AKfycbyUdkCjI2xH6JcSJs-dFRKeuAKQ0hW0snuwCvgWVPb1tVkQFsFtsQXaSORmJWuC-KvV';
const deploymentId = 'AKfycbyG4eXygYUsGKsTTIKPWZJNgXinCjbIxIqUIy14oby8478s5kl1G8KeIpCDKT7ZGCpW';

const submitDisabled = ref(false);
const showSuccessMessage = ref(false);
const showValidationFailMessage = ref(false);
const submitInProgress = ref(false);
const formModel = ref(null);
const cReplyStringPositive = "Zusage";
const cReplyStringNegative = "Absage";
const cRoomOptions = ['Einzelzimmer', 'Doppelzimmer', 'Keines'];

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

    // Scroll to the end of the page after showing the message
    setTimeout(() => {
      window.scrollTo({ top: document.body.scrollHeight, behavior: 'smooth' });
    }, 500);

    // Hide success message after 5 seconds
    setTimeout(() => {
      showSuccessMessage.value = false;
    }, 5000); 

  } catch (error) {
    console.error('Error:', error);
  }
};

watch(form, async () => {
  enableSubmitButton();
})
</script>

<style>
  .centerHeader {text-align: center; margin: 30px 0}
  .person-card {margin: 40px 0}
  .person-card-text {color: lightgray; text-align: center}
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