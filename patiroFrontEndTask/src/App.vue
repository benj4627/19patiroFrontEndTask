<script setup>
//imports
import { ref, onMounted } from 'vue';

//variabler
let patients = ref([]);
let apiUrl = "https://patiro-developer.azurewebsites.net/api/Member";
let selectedPatientDetails = ref(null);
let editMode = ref(false); // Tjekker om redigeringstilstand er aktiv

//funktioner
//async await fetch funktion til at hente patientinformationer
async function fetchData() {
  try {
    const res = await fetch(apiUrl);
    let patientData = await res.json();
    patients.value = patientData;
  } catch (error) {
    console.log('Der er sket fejl', error);
  }
}

//asyns await funktion til at hente specfikke patientdata
async function fetchPatientDetails(id) {
  try {
    const res = await fetch(`https://patiro-developer.azurewebsites.net/api/Member/${id}`);
    let specificPatientData = await res.json();
    selectedPatientDetails.value = specificPatientData;
  } catch (error) {
    console.log('Der er sket fejl ved hentning af patientoplysninger:', error);
  }
}

onMounted(() => {
  fetchData();
});

//funktion til at styre status. switch statement til at vise tekst i stedet for status tal
function getStatusLabel(status) {
  switch (status) {
    case 0: 
      return { label: 'New', class: 'statusNew' };
    case 1: 
      return { label: 'Pending', class: 'statusPending' };
    case 2: 
      return { label: 'Disqualified', class: 'statusDisqualified' };
    case 3: 
      return { label: 'Qualified', class: 'statusQualified' };
    default: 
      return { label: 'Error', class: '' };
  }
}

//funktion til at lukke "detaljer"
function closeDetails() {
  selectedPatientDetails.value = null;
  editMode.value = false;
}

//funktion til at åbne redigeringstilstand
function enterEditMode() {
  editMode.value = true;
}

//funktion til at gemme ændringer i redigeringstilstand
function saveChanges() {
  /// Finder indekset for den patient i listen, hvis ID matcher det valgte patients (p) ID
  const index = patients.value.findIndex(p => p.id === selectedPatientDetails.value.id);
 //Erstatter eksisterende patient med opdaterede oplysninger fra selectedPatientDetails
  patients.value[index] = selectedPatientDetails.value;
  closeDetails();
}
</script>


<template>
  <body>
  <div class="backgroundDiv">
    <div class="tableContainer">
      <table class="table">
        <thead class="tableHeadContent">
          <tr>
            <th>ID</th>
            <th>Navn</th>
            <th>Status</th>
            <th>Handlinger</th>
          </tr>
        </thead>
        <tbody>
          <!-- Loop gennem listen af patienter. Generer en tabelrække for hver patient med deres informationer -->
          <tr v-for="patient in patients" :key="patient.id">
            <td>{{ patient.id }}</td>
            <td>{{ patient.name }}</td>
            <td :class="getStatusLabel(patient.status).class">
              {{ getStatusLabel(patient.status).label }}
            </td>
            <td>
              <button class="detailsButton" @click="fetchPatientDetails(patient.id)">Detaljer</button>
            </td>
          </tr>
        </tbody>
      </table>

      <transition name="fade">
         <!-- Vis detaljer for den valgte patient -->
        <div v-if="selectedPatientDetails" class="specificPatientDetails">
          <h2>Detaljer for {{ selectedPatientDetails.name }}</h2>

          <!-- Hvis vi ikke er i redigeringstilstand, vis patientens detaljer -->
          <div v-if="!editMode">
            <p><span class="bold">Vægt:</span> {{ selectedPatientDetails.weight }} kg</p>
            <p><span class="bold">Højde:</span> {{ selectedPatientDetails.height }} cm</p>
            <p><span class="bold">Adresse:</span> {{ selectedPatientDetails.address }}</p>
            <p><span class="bold">Telefonnummer:</span> {{ selectedPatientDetails.phoneNumber }}</p>
            <button @click="enterEditMode" class="detailsButton">Rediger</button>
            <button @click="closeDetails" class="cancelBtn">Luk</button>
          </div>

          <!-- Hvis redigeringstilstand er aktiv, vis formularer til at redigere patientens information -->
          <div v-else class="editMode">
            <div>
               <!-- Binder input-feltet til patientens informationer, så ændringer opdaterer selectedPatientDetails.xxxx -->
              <label><span class="bold">Navn:</span></label>
              <input v-model="selectedPatientDetails.name" />
            </div>
            <div>
              <label><span class="bold">Vægt:</span></label>
              <input v-model.number="selectedPatientDetails.weight" />
            </div>
            <div>
              <label><span class="bold">Højde:</span></label>
              <input v-model.number="selectedPatientDetails.height" />
            </div>
            <div>
              <label><span class="bold">Adresse:</span></label>
              <input v-model="selectedPatientDetails.address" />
            </div>
            <div>
              <label><span class="bold">Telefonnummer:</span></label>
              <input v-model="selectedPatientDetails.phoneNumber" />
            </div>
            <div>
              <label><span class="bold">Status:</span></label>
              <select v-model.number="selectedPatientDetails.status">
                <option value="0">New</option>
                <option value="1">Pending</option>
                <option value="2">Disqualified</option>
                <option value="3">Qualified</option>
              </select>
            </div>
            <button @click="saveChanges" class="editButton">Gem</button>
            <button @click="closeDetails" class="cancelBtn">Annuller</button>
          </div>
        </div>
      </transition>
    </div>
  </div>
</body>
</template>


<style scoped>
.backgroundDiv {
  background: linear-gradient(to bottom, #0000ff2b, #eaff985f);
  display: flex; 
  align-items: center;
  justify-content: center; 
  padding-top: 5rem;
  gap: 2rem;
}

.tableContainer {
  background-color: rgb(255, 249, 249);
  border-radius: 15px; 
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.164);
  overflow: hidden; 
}

.table {
  width: 100%; 
  border-collapse: collapse; 
}

th, td { 
  padding: 1.5rem 2rem;
  text-align: left;
}

tr {
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.126);
}

th {
  font-size: 1.5rem;
  font-weight: 700;
  margin: 2rem;
}

button {
  padding: .5rem 1rem;
  font-size: 14px;
  color: rgb(255, 237, 237);
  background-color: #479bef;
  border: 2.5px solid #479bef;
  border-radius: 5px;
  cursor: pointer;
  transition: .3s ease-in-out;
  font-weight: 500;
  margin-right: 1rem;
  letter-spacing: .1rem;
}

.detailsButton:hover {
  color: #479bef;
  border: 2.5px solid #479bef;
  background-color: inherit;
}

.editButton {
  background-color: #73b38b;
  border: 2.5px solid  #73b38b;
}

.editButton:hover {
  color: #73b38b;
  border: 2.5px solid #73b38b;
  background-color:  inherit;
}

.closeButton {
  margin: unset;
}

.closeButton:hover {
  color: #479bef;
  border: 2.5px solid #479bef;
  background-color:  inherit;
}

.submitBtn:hover {
  color: #479bef;
  border: 2.5px solid #479bef;
  background-color: inherit;
}

.cancelBtn {
  background-color: #fb7474;
  border: 2.5px solid  #fb7474;
}

.cancelBtn:hover {
  color: #fb7474;
  border: 2.5px solid #fb7474;
  background-color: inherit;
}


.patientName {
  font-weight: bold;
  letter-spacing: .05rem;
}

.statusContainer { 
  margin: 0; 
}

.statusNew, .statusPending, .statusDisqualified, .statusQualified {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1rem 1.5rem;
  margin-top: 1rem;
  font-weight: bold;
  border-radius: 100px;
}

.statusNew {
  color: rgb(255, 255, 255);
  background-color: rgba(84, 166, 242, 0.711); 
}

.statusPending {
  color: #7a5c03; 
  background-color: rgba(255, 255, 0, 0.3); 
}

.statusDisqualified {
  color: #721c24; 
  background-color: rgba(255, 0, 0, 0.2); 
}

.statusQualified {
  color: #155724; 
  background-color: rgba(106, 187, 106, 0.3);
}

.specificPatientDetails {
  background: linear-gradient(to bottom, #dcdcf7, #f6fada);
  z-index: 2;
  border-radius: 20px;
  position: fixed; 
  top: 50%;
  left: 50%; 
  transform: translate(-50%, -50%); 
  padding: 2rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.265);
}

.specificPatientDetails h2 {
  font-weight: bold;
  margin-bottom: 1rem;
}

.bold {
  font-weight: bold;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.7s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

.editPatientDetails {
  background: linear-gradient(to bottom, #dcdcf7, #f6fada);
  z-index: 2;
  border-radius: 20px;
  position: fixed; 
  top: 50%;
  left: 50%; 
  transform: translate(-50%, -50%); 
  padding: 2rem;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.265);
}


.editPatientDetails h2 {
  font-weight: bold;
  margin-bottom: 1.5rem;
  text-align: center;
  font-size: 1.5rem;
}

.editPatientDetails form {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: .5rem;
}

.editPatientDetails label {
  margin-top: 0.5rem;
  font-weight: bold;
}

.editPatientDetails input {
  margin-bottom: 1rem;
  padding: 0.5rem;
  border: 1px solid #d4d4d4;
  border-radius: 5px;
  margin-left: .5rem;
}

.editMode input {
  margin-bottom: 1rem;
  padding: 0.5rem;
  border: 1px solid #d4d4d4;
  border-radius: 5px;
  margin-left: .5rem;
}

.editMode select {
  margin-bottom: 1rem;
  padding: 0.5rem;
  border: 1px solid #d4d4d4;
  border-radius: 5px;
  margin-left: .5rem;
}
</style>