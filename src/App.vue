<script setup>
import { ref, onMounted, nextTick } from 'vue';
import TopBar from './components/TopBar.vue';
import EmployeeRow from './components/EmployeeRow.vue';

// Upon mounting the app, data is fetched from the api, generating weeks in the calendar and calculating initial height of the scroll-thumb.
onMounted(async () => {
  await getBookings();
  fourWeeks.value = generateWeeksFromOffset(currentOffset.value);

  await nextTick(); // We need to wait for the DOM to update with the bookings above before the scrollbar can be rendered properly.
  
  updateScrollbarLayout();
  window.addEventListener('resize', () => { // Resizes the scrollbar as the window-height changes.
    updateScrollbarLayout();
  });
});

const scrollbarTop = ref(0);
const scrollbarHeight = ref(0);
const arrowHeight = ref(35);

const employeeBookings = ref([]);
const currentOffset = ref(0);
const fourWeeks = ref([]);

const scrollArea = ref(null);
const thumbTop = ref(0);
const thumbHeight = ref(0);

const selectedProfession = ref(null);
const professionMap = {
  'Målare': 'Painter',
  'Elektriker': 'Electrician',
  'Murare': 'Mason',
  'Rörmockare': 'Plumber',
  'Snickare': 'Carpenter',
}

// Renders the scrollbar depending on windowheight
function updateScrollbarLayout() {
  const windowHeight = window.innerHeight;
  scrollbarTop.value = windowHeight * 0.44;
  scrollbarHeight.value = windowHeight * 0.5;

  const element = scrollArea.value;
  if (element) {
    const trackHeight = scrollbarHeight.value - arrowHeight.value * 2; // Calculates the track for the thumb to run in.
    thumbHeight.value = Math.max((element.clientHeight / element.scrollHeight) * trackHeight, 30);
    
    const ratio = element.scrollTop / (element.scrollHeight - element.clientHeight);
    thumbTop.value = arrowHeight.value + ratio * (trackHeight - thumbHeight.value);
  }
}

// This function tracks if a profession has been filtered (by klicking a "chip")
function handleProfessionFilter(swedishLabel) {
  const english = professionMap[swedishLabel]; // The label of the chip is in Swedish and get's mapped to English

  if (selectedProfession.value === english) { // Checks if it already is active
    selectedProfession.value = null; // Deactivates the filter
  } else {
    selectedProfession.value = english; // Filters on the chosen chip/profession
  }
}

function onScroll() {
  const element = scrollArea.value;
  if (!element) return;
  const ratio = element.scrollTop / (element.scrollHeight - element.clientHeight);
  const trackHeight = scrollbarHeight.value - arrowHeight.value * 2;
  thumbTop.value = arrowHeight.value + ratio * (trackHeight - thumbHeight.value);
}


// Fetches bookings for employees from the api
async function getBookings() {
  try {
      const response = await fetch('https://yrgo-web-services.netlify.app/bookings');
      if (!response.ok) {
          throw new Error(`Error: ${response.status} - ${response.statusText}`);
      }
      employeeBookings.value = await response.json();
 
  } catch (error) {
    prompt(error.message)
  }
}

// This function creates an array of 4 weeks starting from this week
function generateWeeksFromOffset(offsetInDays = 0) { // Offset is 0 as default if we don't send anything else in.
  const today = new Date(); // Get today's date

  const dayOfWeek = (today.getDay() + 6) % 7; // Sets Sunday as 6, Monday as 0...
  const baseDate = new Date(today);
  baseDate.setDate(today.getDate() - dayOfWeek + offsetInDays); // Sets the basedate as the Monday of the week of today's date

  const weeks = [];
  const dayNames = ['Mån', 'Tis', 'Ons', 'Tors', 'Fre'];

  for (let i = 0; i < 4; i++) { // Loops for every week (4 times) since we have a 4-week view
    const weekStart = new Date(baseDate); // We always start/show a view from Monday of the current week 
    weekStart.setDate(baseDate.getDate() + i * 7); // Jumps a week forward for every iteration

    const dayDates = [];
    for (let d = 0; d < 5; d++) { // Iterates 5 times for the days of the regular work week
      const day = new Date(weekStart); // Begin at the start of the current week in the week-loop 
      day.setDate(weekStart.getDate() + d); // Increments by 1
      dayDates.push(day.toLocaleDateString('sv-SE', { day: '2-digit', month: 'numeric' })); // Adds the date to the array in Swedish with the day-number first and then month ( e.g. 13/5)
    }
    
    const weekNumber = getWeekNumber(weekStart);
    const monthName = getMonthName(weekStart);

    // Adds a week-object with all of the above mentioned data:
    weeks.push({
      month: monthName,
      weekNumber: `Vecka ${weekNumber}`,
      dayNames,
      dayDates
    });
  }

  return weeks;
}

// Gets current week-number by comparing to the week where the 4th of January occurs (first week of the year)
function getWeekNumber(date) {
  const target = new Date(date.valueOf()) // Creates a copy of the date (weekstart, e.g. 12 maj 2025) sent here
  const dayNr = (date.getDay() + 6) % 7 // Converts the standard day-numbers to Sunday as 6, Monday as 0...
  target.setDate(target.getDate() - dayNr + 3) // Jump to Thursday since ISO-standard states that the week-number belongs to the Thursday of the week. 
  const firstThursday = new Date(target.getFullYear(), 0, 4) // Get the first Thursday of the year by setting it to be the week of 4th of January (ISO-standard)
  const diff = target - firstThursday // Gets the difference in milliseconds 
  return 1 + Math.round(diff / (7 * 24 * 60 * 60 * 1000)) // Add 1 (week 1) the rest are (days, hours, minutes, seconds and milliseconds) 
}

// Returns the month of the date as a text-string in Swedish with the first letter as uppercase
function getMonthName(date) {
  return date.toLocaleDateString('sv-SE', { month: 'long' }).charAt(0).toUpperCase() +
         date.toLocaleDateString('sv-SE', { month: 'long' }).slice(1)
}

// Makes it possible to jump back/forth by either a month/week in the calendar  
function navigate(direction, viewMode) {
  const days = viewMode === 'month' ? 28 : 7; // If "month" = 28 days (4 weeks) else 7 days (week)
  currentOffset.value += direction === 'forward' ? days : -days; // If forward we add days, else the opposite. Also keeps track of how much we differ from the current date
  fourWeeks.value = generateWeeksFromOffset(currentOffset.value);
}

</script>

<template>
  <div class="scrollable-container" ref="scrollArea" @scroll="onScroll">
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
        <!-- Show if no filter is select, else only the filtered ones  -->
      </div>
      
      <div class="end-color"></div>
    </main>
    <div class="custom-scrollbar" :style="{ top: scrollbarTop + 'px', height: scrollbarHeight + 'px' }">
      <div class="scroll-arrow-up" :style="{ height: arrowHeight + 'px' }">
        <img src="./assets/icons/scroll-up.png" alt="scroll-up" class="scroll-icon-up">
      </div>
      <div class="scroll-thumb" :style="{ top: thumbTop + 'px', height: thumbHeight + 'px' }"></div>
      <div class="scroll-arrow-down" :style="{ height: arrowHeight + 'px' }">
        <img src="./assets/icons/scroll-down.png" alt="scroll-up" class="scroll-icon-down">
      </div>
    </div>
  </div>
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
  min-width: 1300px;
  overflow: auto;
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

.scrollable-container {
  overflow-y: scroll;
  scrollbar-width: none;
  -ms-overflow-style: none;
  position: relative;
  height: 100vh;
  min-width: 1300px;
}

.scrollable-container::-webkit-scrollbar {
  width: 0px;
  background: transparent;
}

.custom-scrollbar {
  position: fixed;
  right: 20px;
  width: 40px;
  background-color: #D6DDD6;
  z-index: 100;
  border-radius: 5px;
}

.scroll-arrow-up,
.scroll-arrow-down {
  background-color: #9bad9b;
  display: flex;
  align-items: center;
  justify-content: center;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}

.scroll-arrow-up {
  position: absolute;
  left: 0;
  width: 100%;
}

.scroll-arrow-down {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
}

.scroll-thumb {
  position: absolute;
  width: 10px;
  left: 15px;
  background-color: #9bad9b;
  border-radius: 8px;
}

</style>
