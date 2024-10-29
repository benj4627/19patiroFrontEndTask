<script setup>
//imports
import { ref, onMounted } from 'vue';

//async await fetch funktion
let patients = ref([])
let apiUrl = "https://patiro-developer.azurewebsites.net/api/Member"

  async function fetchData() {
    try {
      const res = await fetch(apiUrl)
      let patientData = await res.json()
      console.log(patientData)
      patients.value = patientData;
    } catch (error) {
      console.log('Der er sket fejl', error)
    }
  }
  onMounted(() => {
      fetchData();
    });

//funktion til at vise label status
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


</script>

<template>
  <body>
    <div class="backgroundDiv">
      <div class="tableContainer">
        <table class="table">
          <thead class="tableHeadContent">
            <tr>
              <th class="patientIDTitle">ID</th>
              <th class="patientNameTitle">Navn</th>
              <th class="patientStatusTitle">Status</th>
              <th class="patientActionTitle">Handlinger</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="patient in patients" :key="patient.id">
              <td class="patientID">{{ patient.id }}</td>
              <td class="patientName">{{ patient.name }}</td>
              <td :class="getStatusLabel(patient.status).class"> <div class="statusContainer">
                {{ getStatusLabel(patient.status).label }}</div>
              </td>
              <td class="patientAction">
                <button class="detailsButton" @click="viewDetails(patient.id)">Detaljer</button>
                <button class="editButton" @click="editPatient(patient.id)">Rediger</button>
              </td>
            </tr>
          </tbody>
        </table>
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
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.126);

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
  background-color:  rgb(255, 237, 237);
}

.editButton {
  background-color: #73b38b;
  border: 2.5px solid  #73b38b;
}

.editButton:hover {
  color: #73b38b;
  border: 2.5px solid #73b38b;
  background-color:  rgb(255, 237, 237);
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
}

.statusNew {
  color: rgb(255, 255, 255);
  background-color: rgba(84, 166, 242, 0.711); 
  font-weight: bold;
  border-radius: 20px;
}

.statusPending {
  color: #7a5c03; 
  background-color: rgba(255, 255, 0, 0.3); 
  font-weight: bold;
  border-radius: 20px;
}

.statusDisqualified {
  color: #721c24; 
  background-color: rgba(255, 0, 0, 0.2); 
  font-weight: bold;
  border-radius: 20px;
}

.statusQualified {
  color: #155724; 
  background-color: rgba(106, 187, 106, 0.3);
  font-weight: bold;
  border-radius: 20px;
}
</style>