<script setup>
import { ref, watch } from 'vue';
import Chip from './Chip.vue';
import Week from './Week.vue';

const props = defineProps({
  companyName: String,
  dates: Array,
  selectedProfession: String,
  professionMap: Object
})

const monthIsActive = ref(true);
const monthIsDisabled = ref(false);

const weekIsActive = ref(false);
const weekIsDisabled = ref(true);

const dayIsActive = ref(false);
const dayIsDisabled = ref(true);

const professionChips = ['Målare', 'Elektriker', 'Murare', 'Rörmockare', 'Snickare'];

const professionColors = {
  Målare: 'rgba(252, 142, 74, 1)',
  Elektriker: 'rgba(139, 179, 77, 1)',
  Murare: 'rgba(255, 238, 0, 1)',
  Rörmockare: 'rgba(116, 185, 205, 1)',
  Snickare: 'rgba(180, 114, 190, 1)',
}

const bookedChips = ['50 %', '100 %', 'Prel. bokad', 'Tillgänglig', 'Ledig'];

const bookedColors = {
  '50 %': 'rgba(212, 232, 111, 1)',
  '100 %': 'rgba(212, 232, 111, 1)',
  'Prel. bokad': 'rgba(217, 217, 217, 1)',
  Tillgänglig: 'rgba(172, 197, 165, 1)',
  Ledig: 'rgba(134, 144, 131, 1)',
}

const currentPeriod = ref('month');

function activatePeriod(period) {
  currentPeriod.value = period;
  monthIsActive.value = period === 'month';
  weekIsActive.value = period === 'week';
  dayIsActive.value = period === 'day';

  monthIsDisabled.value = period !== 'month';
  weekIsDisabled.value = period !== 'week';
  dayIsDisabled.value = period !== 'day';
}

const activeWeekIndex = ref(0);

function setActiveWeek(index) {
  activeWeekIndex.value = index;
}

const emit = defineEmits(['select-profession', 'navigate']);

function handleChipClick(profession) {
  emit('select-profession', profession);
}

function handleArrowClick(direction) {
  emit('navigate', direction, currentPeriod.value);
}

</script>

<template>
  <div class="topBar poppins-regular">
    <div class="calendar-section">
      <h1 class="main-header">{{ companyName }}</h1>
      <div class="calendarView">
        <img src="../assets/icons/arrow-down-icon.png" alt="down arrow" class="arrow-down-icon">
        <select name="month" id="calendarMonth">
          <option value="april">April 2025</option>
          <option value="maj">Maj 2025</option>
          <option value="juni">Juni 2025</option>
        </select>
        <div class="changeDates">
          <button class="arrow-button" @click="handleArrowClick('backward')">
            <img src="../assets/icons/arrow-left-icon.png" alt="left arrow" class="arrow-left-icon">
          </button>
          <div class="choosePeriod poppings-regular">
            <button :class="['period-button', { active: monthIsActive, disabled: monthIsDisabled }]"
              @click="activatePeriod('month')">Månad</button>
            <button :class="['period-button', { active: weekIsActive, disabled: weekIsDisabled }]"
              @click="activatePeriod('week')">Vecka</button>
            <button :class="['period-button', { active: dayIsActive, disabled: dayIsDisabled }]"
              @click="activatePeriod('day')">Dag</button>
          </div>
          <button class="arrow-button" @click="handleArrowClick('forward')">
            <img src="../assets/icons/arrow-right-icon.png" alt="right arrow" class="arrow-right-icon">
          </button>
        </div>
      </div>
    </div>
    <div class="chip-wrapper">
      <p>Sortera efter</p>
      <div class="chip-section">
        <div class="profession-category">
          <Chip v-for="(chip, index) in professionChips" 
            :key="index" 
            :label="chip"
            :class="{ active: selectedProfession === professionMap[chip] }"
            @click="handleChipClick(chip)">

            <template #default="{ label }">
              <div class="profession-color" :style="{ backgroundColor: professionColors[label] || 'black' }">
              </div>
              <span>{{ label }}</span>
            </template>
          </Chip>
        </div>
        <div class="booked-category">
          <Chip v-for="(chip, index) in bookedChips" :key="index" :label="chip" @click="handleChipClick(chip)">

            <template #default="{ label }">
              <div :class="[label === '50 %' ? 'striped-color' : 'booked-color']"
                :style="{ backgroundColor: bookedColors[label] || 'black' }">
              </div>
              <span>{{ label }}</span>
            </template>
          </Chip>
        </div>
      </div>
    </div>
    <div class="date-section">
      <div class="date-options">
        <div class="month-week-wrapper">
          <span>Månad</span>
          <span>Vecka</span>
        </div>
        <div class="day">Dag</div>
      </div>
        <Week v-for="(date, index) in dates"
          :key="index"
          :dates="date"
          :is-active="index === activeWeekIndex"
          @click="setActiveWeek(index)">
        </Week>
    </div>
  </div>
</template>

<style scoped>
.topBar {
  background-color: rgb(114, 133, 109);
  width: 100%;
  min-width: 1400px;
  overflow: auto;
}

.calendar-section {
  display: flex;
  justify-content: space-between;
}

.main-header {
  max-width: 250px;
  color: white;
  font-weight: bold;
  line-height: 1.1;
}

.calendarView {
  display: flex;
  flex-direction: column;
  align-items: center;
}

#calendarMonth {
  padding: .5rem 5rem;
  appearance: none;
  border-style: none;
  border-radius: 5px;
  color: rgba(103, 114, 100, 1);
}

#calendarMonth:hover {
  cursor: pointer;
}

.changeDates {
  width: 100%;
  display: flex;
  align-items: center;
  padding-top: .5rem;
  gap: .25rem;
}

.arrow-button {
  background-color: white;
  border-style: none;
  border-radius: 5px;
  height: 30px;
  width: 28px;
}

.arrow-button:hover {
  cursor: pointer;
}

.arrow-button:active {
  background-color: rgb(205, 205, 205);
}

.choosePeriod {
  display: flex;
  align-items: center;
  justify-content: space-around;
  height: 32px;
  width: 220px;
  background-color: white;
  border-radius: 5px;
}

.period-button {
  height: 22px;
  width: 60px;
  background-color: white;
  border-style: none;
  border-radius: 5px;
}

.period-button:hover {
  cursor: pointer;
}

.period-button.active {
  background-color: rgba(172, 197, 165, 1);
}

.chip-wrapper {
  padding-top: 1.75rem;

  p {
    color: white;
    font-weight: 500;
    font-size: 95%;
  }
}

.chip-section {
  display: flex;
  justify-content: space-between;
  padding-top: .5rem;
}

.profession-category {
  display: flex;
  gap: .5rem;

  .chip:hover {
    border: 2px solid black;
    cursor: pointer;
  }
}

.profession-color {
  position: relative;
  width: 10px;
  height: 10px;
  border-radius: 50px;
  top: 4.5px;
  left: -7px;
}

.booked-category {
  display: flex;
  gap: .5rem;
}

.striped-color {
  position: relative;
  width: 16px;
  height: 16px;
  top: 1.5px;
  left: -7px;
  border-radius: 3px;
  background-image: repeating-linear-gradient(90deg,
      rgba(212, 232, 111, 1),
      rgba(212, 232, 111, 1) 2px,
      white 2px,
      white 4.5px);
  display: block;
  flex: 0 0 16px;
}

.booked-color {
  position: relative;
  width: 16px;
  height: 16px;
  top: 1.5px;
  left: -7px;
  border-radius: 4px;
}

.date-section {
  display: flex;
  gap: 1rem;
  height: 160px;
  background-color: rgb(80, 69, 61);
  margin-top: 1rem;
  margin-bottom: 0;
  padding: 1rem 2rem 0 0;
  border-top-left-radius: 20px;
  border-top-right-radius: 20px;
  color: white;
  font-weight: 500;
}

.date-options {
  flex: .65;
  display: flex;
  justify-content: space-between;
  flex-direction: column;
  height: 100%;
  padding-left: 1rem;
}

.month-week-wrapper {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: end;
  height: 100%;
  
  span {
    text-align: left;  
  }
}

.day {
  flex: 1;
  display: flex;
  align-items: end;
  justify-content: left;
  padding-bottom: .5rem;
}

</style>