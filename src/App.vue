<script setup>
import { ref, onMounted } from 'vue';
import TopBar from './components/TopBar.vue';
import EmployeeRow from './components/EmployeeRow.vue';

const employeeBookings = ref([]);

async function getBookings() {
  try {
      const response = await fetch('https://yrgo-web-services.netlify.app/bookings');
      if (!response.ok) {
          throw new Error(`Error: ${response.status} - ${response.statusText}`);
      }
      employeeBookings.value = await response.json();
 
  } catch (error) {
      console.log(error.message);
  }
}

onMounted(() => {
  getBookings()
})

const fourWeeks = ref([
  {
    month: 'April',
    weekNumber: 'Vecka 25',
    dayNames: ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'],
    dayDates: ['12/5', '13/5', '14/5', '15/5', '16/5']
  },
  {
    month: 'April/Maj',
    weekNumber: 'Vecka 26',
    dayNames: ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'],
    dayDates: ['19/5', '20/5', '21/5', '22/5', '23/5']
  },
  {
    month: 'Maj',
    weekNumber: 'Vecka 27',
    dayNames: ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'],
    dayDates: ['26/5', '27/5', '28/5', '29/5', '30/5']
  },
  {
    month: 'Maj',
    weekNumber: 'Vecka 28',
    dayNames: ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'],
    dayDates: ['2/6', '3/6', '4/6', '5/6', '6/6']
  }
])

const selectedProfession = ref(null);

const professionMap = {
  'Målare': 'Painter',
  'Elektriker': 'Electrician',
  'Murare': 'Mason',
  'Rörmockare': 'Plumber',
  'Snickare': 'Carpenter',
}

function handleProfessionFilter(swedishLabel) {
  const english = professionMap[swedishLabel];

  if (selectedProfession.value === english) {
    selectedProfession.value = null;
  } else {
    selectedProfession.value = english;
  }
}

</script>

<template>
  <header>

    <div class="wrapper">
      <TopBar 
        companyName="Svenssons Hantverk AB" 
        :dates="fourWeeks"
        :selectedProfession="selectedProfession"
        :professionMap="professionMap"
        @select-profession="handleProfessionFilter"
      />
    </div>

  </header>

  <main class="poppins-regular">
    <div>
      <img src="./assets/icons/account-icon.png" alt="account icon" class="account-icon side-icon">
      <img src="./assets/icons/calendar-icon.png" alt="calendar icon" class="calendar-icon side-icon">
    </div>

    <div class="booking-view">
      <h4>Anställda</h4>
      <EmployeeRow
        v-for="(employee, index) in employeeBookings"
        :key="index"
        :employee="employee"
        :dates="fourWeeks"
        v-show="!selectedProfession || employee.professions.includes(selectedProfession)"
      />

    </div>
    
    <div class="end-color"></div>
  </main>
</template>

<style scoped>
header {
  padding: 2rem 4rem 3rem 4rem;
  background-color: rgb(114, 133, 109);
  width: 100%;
  padding-bottom: 0;
  position: fixed;
  z-index: 100;

  .wrapper {
    width: 100%;
  }
}

main {
  width: 100%;
  background-color: rgb(114, 133, 109);
  padding: 0 4rem 3rem 4rem;
  margin-top: 13.3rem;
}

.side-icon:hover {
  cursor: pointer;
}

.booking-view {
  background-color: white;
  padding: 11rem 2rem 2rem 1rem ;

  h4 {
    padding-bottom: .5rem;
  }
}

.end-color {
  width: 100%;
  background-color:  rgb(80, 69, 61);
  padding: 1.5rem 0;
}
</style>
