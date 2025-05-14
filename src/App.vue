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
  getBookings();
})

function generateWeeksFromOffset(offsetInDays = 0) {
  const today = new Date();

  // Gå till måndag den här veckan
  const dayOfWeek = (today.getDay() + 6) % 7;
  const baseDate = new Date(today);
  baseDate.setDate(today.getDate() - dayOfWeek + offsetInDays);

  const weeks = [];
  const dayNames = ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'];

  for (let i = 0; i < 4; i++) {
    const weekStart = new Date(baseDate);
    weekStart.setDate(baseDate.getDate() + i * 7);

    const dayDates = [];
    for (let d = 0; d < 5; d++) {
      const day = new Date(weekStart);
      day.setDate(weekStart.getDate() + d);
      dayDates.push(day.toLocaleDateString('sv-SE', { day: '2-digit', month: 'numeric' }));
    }
    
    const weekNumber = getWeekNumber(weekStart);
    const monthName = getMonthName(weekStart);

    weeks.push({
      month: monthName,
      weekNumber: `Vecka ${weekNumber}`,
      dayNames,
      dayDates
    });
  }

  return weeks;
}


function getWeekNumber(date) {
  const target = new Date(date.valueOf())
  const dayNr = (date.getDay() + 6) % 7
  target.setDate(target.getDate() - dayNr + 3)
  const firstThursday = new Date(target.getFullYear(), 0, 4)
  const diff = target - firstThursday
  return 1 + Math.round(diff / (7 * 24 * 60 * 60 * 1000))
}

function getMonthName(date) {
  return date.toLocaleDateString('sv-SE', { month: 'long' }).charAt(0).toUpperCase() +
         date.toLocaleDateString('sv-SE', { month: 'long' }).slice(1)
}

const currentOffset = ref(0);
const fourWeeks = ref([]);

onMounted(() => {
  fourWeeks.value = generateWeeksFromOffset(currentOffset.value);
});


function navigate(direction, viewMode) {
  const days = viewMode === 'month' ? 28 : 7;
  currentOffset.value += direction === 'forward' ? days : -days;
  fourWeeks.value = generateWeeksFromOffset(currentOffset.value);
}


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
        @navigate="navigate"
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
