<!-- https://pictogrammers.github.io/@mdi/font/2.0.46/ -->


<template>
  <v-container class="container-background" fluid>
    <div class="image-yes-container">
      <img src="../assets/wir-sagen-ja.png" alt="Wir sagen ja">
      <div class="mm-overlay">HOCHZEIT VON<br>MAILINA UND MARKUS</div>
      <div class="date-overlay">24<br>05<br>24</div>
    </div>

    <!-- FORM -->
    <v-form ref="formModel" @submit.prevent="handleSubmit">
      <div class="form">

        
        <v-card class="sheet"
          :elevation="5"
          border
          rounded
        >
        <!-- Basics -->
          <template v-slot:title>
            <div style="text-align: left">&nbsp&nbspUNSER TAG<br><br></div>

            <!-- Timeline -->
            <v-timeline align="start" truncate-line="start">
              <v-timeline-item 
                v-for="(item, i) in timeLineItems"
                :key="i"
                :dot-color="item.color"
                :icon="item.icon"
                size="x-small"
              >
              <template v-slot:opposite>
                <!-- <div :class="`pt-1 headline font-weight-bold text-${item.color}`" v-text="item.time"></div> -->
                <div :class="`pt-1 headline font-weight-bold `" v-text="item.time"></div>
              </template>
              <div>
                <!-- <div :class="['timeline-header', `bg-${item.color}`]" > -->
                <div class="timeline-header" >
                  {{ item.activity }}
                </div>
                <div class="text--secondary caption timeline-desc">
                  <p>{{ item.description }}</p>
                </div>
              </div>
              </v-timeline-item>
            </v-timeline>
          </template>
        </v-card>

        <v-card class="sheet"
          prepend-icon="mdi-star-outline mdi-spin"
          :elevation="5"
          border
          rounded
        >
        <!-- Basics -->
        <template v-slot:title>
          <span class="make-uppercase">Rückmeldung zur Einladung</span>
        </template>
        <!-- an welche Adresse dürfen wir weitere Information, Fotos, etc. senden</v-expansion-panel-text -->

        <v-text-field
            v-model="form.email"
            label="Kontakt-Email"
            type="email"
            :rules="[v => !!v || 'Bitte gib eine E-Mailadresse an!']"
          ></v-text-field>

          <v-select class="styledselect"
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
            label="Anmerkung"
            persistent-hint
            auto-grow
            v-if="form.kidsCount > 0"
          ></v-textarea>

        </v-card>

        <!-- Guest 1 -->
        <v-card
          prepend-icon="mdi-heart-outline mdi-spin"
          :elevation="5"
          border
          rounded
          class="sheet"
          v-if="form.rsvp === cReplyStringPositive"
        >
        <template v-slot:title>
          <span class="make-uppercase">Gast 1</span>
        </template>

        <v-text-field
            v-model="form.guestName1"
            label="Vorname Nachname"
          ></v-text-field>

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
          prepend-icon="mdi-heart-outline mdi-spin"
          :elevation="5"
          border
          rounded
          class="sheet"
          v-if="(form.rsvp === cReplyStringPositive) && (form.guestCount > 1)"
        >
        <template v-slot:title>
          <span class="make-uppercase">Gast 2</span>
        </template>

        <v-text-field
            v-model="form.guestName2"
            label="Vorname Nachname"
          ></v-text-field>

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
          prepend-icon="mdi-star-outline mdi-spin"
          :elevation="5"
          border
          rounded
          v-if="form.rsvp === cReplyStringPositive"
        >
          <!-- Basics -->
          <template v-slot:title>
            <span class="make-uppercase">Zimmer und Wünsche</span>
          </template>
          <div class="plain-text">Es stehen genügend Zimmer mit Frühstück für alle Gäste zur Verfügung. Euer Geschenk an uns ist eure Übernachtung.</div>

          <v-text-field
            v-model="form.bookRoom1"
            label="Einzelzimmer"
            type="number"
            min="0"
            max="2"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-text-field>
          
          <v-text-field
            v-model="form.bookRoom2"
            label="Doppelzimmer"
            type="number"
            min="0"
            max="2"
            v-if="form.rsvp === cReplyStringPositive"
          ></v-text-field>

          <v-textarea
            v-model="form.additionalInfos"
            label="Weitere Anmerkungen, Liederwünsche, etc."
            persistent-hint
            auto-grow
          ></v-textarea>

        </v-card class="sheet">
          <v-btn
            type="submit" 
            :disabled="submitDisabled"
            color="primary"
            :loading="submitInProgress"
            class="button-container"
          >
            Antwort Absenden
          </v-btn>
        </div>
      </v-form>
      <div class="uuid-style">
            Antwort-ID: {{ form.uuid }}
      </div>

    <!-- UUID -->
    <!-- <v-card class="mx-auto person-card" width="50%">
      <v-card-text class="person-card-text">
        Antwort-ID: {{ form.uuid }}
      </v-card-text>
    </v-card> -->

    <v-alert v-if="showSuccessMessage" type="success" dismissible class="alert-message">
      Danke für die Rückmeldung<span v-if="form.guestName1">, {{ form.guestName1 }}</span>!
    </v-alert>

    <v-alert v-if="showValidationFailMessage" type="error" dismissible class="alert-message">
      Bitte die Pflichtfelder ausfüllen!
    </v-alert>
  </v-container>
</template>

<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
import { v4 as uuidv4 } from 'uuid'; 
// import '@mdi/font/css/materialdesignicons.min.css';

const form = reactive({
  email: '',
  rsvp: cReplyStringPositive,
  kidsCount: 0,
  kidsSpecialInfo: '',
  guestCount: 2,
  guestName1: '',
  foodPreference1: '',
  customFoodPreference1: '',
  guestName2: '',
  foodPreference2: '',
  customFoodPreference2: '',
  bookRoom1: 0,
  bookRoom2: 1,
  additionalInfos: '',
  uuid: '' 
});

//const deploymentId = 'AKfycbyG4eXygYUsGKsTTIKPWZJNgXinCjbIxIqUIy14oby8478s5kl1G8KeIpCDKT7ZGCpW';
const deploymentId = 'AKfycbwczoKAMcXjCS4wbmY_Z63xiyLxVYw968k0jPfxhb8mfH2aAlTLJr50UVZtejvJL9KL';
const submitDisabled = ref(false);
const showSuccessMessage = ref(false);
const showValidationFailMessage = ref(false);
const submitInProgress = ref(false);
const formModel = ref(null);
const cReplyStringPositive = "Zusage";
const cReplyStringNegative = "Absage";
const cRoomOptions = ['1', '2', '0'];

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

const timeLineItems = ref([
  { time: '13:00 Uhr', activity: 'Ankunft', description: 'Die Gäste treffen in der malerischen Kulisse der Burg Deutschlandsberg ein, voller Vorfreude auf das bevorstehende Fest. → Burgplatz 1, 8530 Deutschlandsberg', color: 'teal' },
  { time: '14:00 Uhr', activity: 'Kirche', description: 'Weiter geht es zur nahegelegenen Wolfgangikirche, wo sich das Paar das Ja-Wort geben wird. Da Parkplätze nur sehr eingeschränk vorhanden sind, währen Fahrgemeinschaften vorteilhaft. → Kruckenberg 19, 8541 Schwanberg (12\' Fahrtzeit)', color: 'green' },
  { time: '14:30 Uhr', activity: 'Trauung', description: 'Im kleinen aber feinen Rahmen geben sich Mailina und Markus ihr Versprechen der Liebe und Treue, umgeben von Familie und Freunden.', color: 'blue' },
  { time: '16:00 Uhr', activity: 'Burg', description: 'Zurück auf der Burg, wird das Brautpaar mit einem herzlichen Empfang von den Gästen begrüßt. Die Burg bietet zahlreiche Entdeckungsmöglichkeiten und die malerische Kulisse ist wie geschaffen für bezaubernde Fotoaufnahmen.', color: 'indigo' },
  { time: '17:00 Uhr', activity: 'Museum', description: 'Optional: Für geschichtsinteressierte Gäste bietet sich z.B. während des Fotoshootings die Möglichkeit, das Burgmuseum zu erkunden und in die Vergangenheit einzutauchen.', color: 'deep-purple' },
  { time: '18:30 Uhr', activity: 'Hochzeitsessen', description: 'Ein festliches Abendessen im romatischen und mittelalterlichem Ambiente, bei dem die Gäste in den Genuss exquisiter Speisen und erlesener Weine kommen.', color: 'purple' },
  { time: '20:30 Uhr', activity: 'Hochzeitstanz', description: 'Der erste Tanz des Brautpaares eröffnet den Tanzreigen, ein unvergesslicher Moment voller Liebe und Harmonie.', color: 'pink' },
  { time: '24:00 Uhr', activity: 'Mitternachtsimbiss', description: 'Ein leckerer Mitternachtsimbiss versorgt die Gäste mit neuer Energie, um die Nacht durchzutanzen.', color: 'red' },
  { time: 'Open End', activity: 'Party', description: 'Die Feier geht weiter und die Tanzfläche bebt bis in den frühen Morgenstunden alle in die weichen Federkissen fallen.', color: 'orange' }
]);



watch(form, async () => {
  enableSubmitButton();
})
</script>

<style>
  @font-face {
    font-family: 'Millano';
    src: local('Millano'), url('../assets/Millano.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
  }
  @font-face {
    font-family: 'Geo';
    src: local('Geo'), url('../assets/GeosansLight.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
  }
  @font-face {
    font-family: 'MrEaves';
    src: local('MrEaves'), url('../assets/MrEavesXLModOT-Reg.ttf') format('truetype');
    font-weight: normal;
    font-style: normal;
  }

 @keyframes sparkle_v0 {
  0%, 100% {
    color: white;
    text-shadow: none;
  }
  50% {
    color: #cccccc; /* Light gray for the sparkle effect */
    text-shadow: 0 0 10px #ffffff, 0 0 20px #ffffff, 0 0 30px #e0e0e0, 0 0 40px #e0e0e0;
  }
} 

@keyframes sparkle {
  0%, 20% { /*  peak */
    color: white;
    text-shadow: none;
  }
  20%, 60% { /* pause */
    color: #cccccc;
    text-shadow: 0 0 10px #ffffff, 0 0 20px #ffffff, 0 0 30px #e0e0e0, 0 0 40px #e0e0e0;
  }
  60%, 100% { /* complete fade */
    color: white;
    text-shadow: none;
  }
}

.centerHeader {
  font-family: 'Millano';
  font-size: 300%;
  text-align: center;
  margin: 30px 0;
  color: white;
  letter-spacing: 0;
  font-weight: 100;
  animation: sparkle 4s infinite linear;
}

.centerHeader .line {
  display: block;
  margin-bottom: 30px; /* spacing between lines */
}

.centerHeader .line:last-child {
  margin-bottom: 90; /* extra space at the bottom */
}

.person-card {margin: 40px 0;}
.person-card-text {color: lightgray; text-align: center;}
.alert-message {margin: 40px 0;}
.form {
  display: flex;
  gap: 35px;
  flex-direction: column;
}

.container-background {
  background-image: linear-gradient(transparent 50%, rgba(255, 255, 255, 0.9) 90%), url('../assets/bg.jpg');
  background-attachment: fixed;
  /* background-position: left 50%; */
  background-position: center 50%; /* Move background up */
  background-repeat: no-repeat;
  background-size: cover;
  min-height: 100vh;
}

.form .sheet {
  color:  rgba(25, 25, 25, 1);
  padding: 15px;
  width: 50%;
  margin-left: 0;
  margin-right: auto;
  background-color: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(5px);
}

.uuid-style {
  width: 50%; 
  margin-top: 1em;
  text-align: center;
  font-size: small;
  color:  rgba(125, 125, 125, 0.25);
}

.button-container {
  width: 50%; 
}

  /* Media query for tablets in portrait mode */
  @media (min-width: 701px) and (max-width: 1024px) {
    .form .sheet {
      width: 60%; /* tablets in portrait */
      margin-left: 0; 
      margin-right: auto;
      padding: 15px; 
    }
    .uuid-style {
      width: 60%; 
    }
    .button-container {
      width: 60%; 
    }
    .image-yes-container img {
      max-width: 70%;
    }
  }

  /* Media query for mobile devices */
  @media (max-width: 700px) {
    .form .sheet {
      width: 100%; /* Full width for mobile devices */
      margin: 0; /* Remove  margins for mobile */
      padding: 10px; /*  padding for mobile  */
    }
    .uuid-style {
      width: 100%; 
    }
    .button-container {
      width: 100%; 
    }
    .image-yes-container img {
      max-width: 80%;
    }
  }

  @media only screen 
  and (orientation: landscape) 
  and (-webkit-min-device-pixel-ratio: 2), only screen 
  and (orientation: landscape) 
  and (min--moz-device-pixel-ratio: 2), only screen 
  and (orientation: landscape) 
  and (-o-min-device-pixel-ratio: 2/1), only screen 
  and (orientation: landscape) 
  and (min-device-pixel-ratio: 2), only screen 
  and (orientation: landscape) 
  and (min-resolution: 192dpi), only screen 
  and (orientation: landscape) 
  and (min-resolution: 2dppx), only screen 
  and (orientation: landscape) 
  and (-webkit-min-device-pixel-ratio: 3), only screen 
  and (orientation: landscape) 
  and (min--moz-device-pixel-ratio: 3), only screen 
  and (orientation: landscape) 
  and (-o-min-device-pixel-ratio: 3/1), only screen 
  and (orientation: landscape) 
  and (min-device-pixel-ratio: 3), only screen 
  and (orientation: landscape) 
  and (min-resolution: 288dpi), only screen 
  and (orientation: landscape) 
  and (min-resolution: 3dppx) {

  .container-background {
    background-image: linear-gradient(transparent 50%, rgba(255, 255, 255, 0.9) 90%), url('../assets/bg-center.jpg'); 
    background-attachment: fixed;
    background-position: center 50%; 
    background-repeat: no-repeat;
    background-size: cover;
    min-height: 100vh;
  }
}

@media only screen 
  and (orientation: portrait) 
  and (-webkit-min-device-pixel-ratio: 2), only screen 
  and (orientation: portrait) 
  and (min--moz-device-pixel-ratio: 2), only screen 
  and (orientation: portrait) 
  and (-o-min-device-pixel-ratio: 2/1), only screen 
  and (orientation: portrait) 
  and (min-device-pixel-ratio: 2), only screen 
  and (orientation: portrait) 
  and (min-resolution: 192dpi), only screen 
  and (orientation: portrait) 
  and (min-resolution: 2dppx), only screen 
  and (orientation: portrait) 
  and (-webkit-min-device-pixel-ratio: 3), only screen 
  and (orientation: portrait) 
  and (min--moz-device-pixel-ratio: 3), only screen 
  and (orientation: portrait) 
  and (-o-min-device-pixel-ratio: 3/1), only screen 
  and (orientation: portrait) 
  and (min-device-pixel-ratio: 3), only screen 
  and (orientation: portrait) 
  and (min-resolution: 288dpi), only screen 
  and (orientation: portrait) 
  and (min-resolution: 3dppx) {

  .container-background {
    background-image: linear-gradient(transparent 50%, rgba(255, 255, 255, 0.9) 90%), url('../assets/bg-mobile-portrait.jpg'); 
    background-attachment: fixed;
    background-position: center 50%; 
    background-repeat: no-repeat;
    background-size: cover;
    min-height: 100vh;
  }
}

.image-yes-container {
    position: relative; /* Establish a positioning context for absolute positioning inside */
    text-align: center; /* Center the image horizontally */
    margin-top: 50px; /* Add space on the top, adjust as needed */
    margin-bottom: 100px; /* Add space on the bottom, adjust as needed */
 }

.image-yes-container img {
    max-width: 60%; /* Maximum width of the image is 50% of its container */
    height: auto; /* Maintain the aspect ratio */
    display: block; /* Treat the image as a block-level element */
    margin-left: auto; /* These two margin properties will center the image */
    margin-right: auto;
    opacity: 0.8;
    filter: blur(0.4px) drop-shadow(0 0 12px rgba(255, 255, 255, 1));
}

.mm-overlay {
  position: absolute;
  top: 70%; 
  left: 58%; 
  transform: translate(-50%, -50%);
  color: #fff; 
  font-size: 2vw;
  text-shadow: 0 0 5px rgba(25, 5, 0, 0.6); /* Correct property for text shadow */
}

.date-overlay {
  position: absolute;
  top: 25%; 
  left: 57.6%; 
  transform: translate(-50%, -50%);
  color: rgba(30, 20, 0, 0.5);
  font-size: 3vw; 
  line-height: 1.2;
}

.form .sheet
{
  font-family: 'Geo';
  color: black;
  font-weight:bolder;
}

.v-card-title
{
  font-family: 'MrEaves';
  color: black;
  font-weight:bolder;
}

.v-list-item-title { /* Dropdown item font*/
  font-family: 'Geo';
}


.v-list /* Dropdown bg*/
{
  background-color: transparent !important;
  backdrop-filter: blur(5px);
}

.v-timeline-item {
  word-wrap: break-word;
  white-space: normal;
}

.timeline-header{
  transform: translateY(-0.3em);
  padding: 0.3em 0.3em 0.3em 0.3em;
  word-wrap: break-word;
  white-space: normal;
  font-size: 16px;
  line-height: 1.4; 
  background-color: rgba(255, 255, 255, 0.2);
}

.timeline-desc{
  padding: 0.3em 0.3em 0.3em 0.3em;
  word-wrap: break-word;
  white-space: normal;
  font-size: 14px;
  line-height: 1.2; 
  font-family: 'Geo';
}

.text-h8.v-card-title, .caption {
  overflow-wrap: break-word;
  word-break: break-word;
  white-space: normal;
}

.v-timeline-item__opposite {
  transform: translateY(-0.45em);
  text-shadow: 1px 1px 1px rgba(255, 255, 255, 0.45); 
}

.make-uppercase {
  text-transform: uppercase;
}

.v-btn.bg-primary {
  background-color: rgba(125, 24, 46, 0.9) !important;
}

.v-card-item {
  text-align: left !important;
  padding-left: 0.2em !important;
}


.v-card-item__prepend {
  transform: translateY(-0.06em);  
}

.plain-text {
  padding: 0.1em 0.3em 1.3em 0.6em;
  color: rgba(75, 75, 75, 1);
}


</style>