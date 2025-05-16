<script setup>
import { computed } from 'vue'
import Employee from './Employee.vue';

const props = defineProps({
    employee: Object,
    dates: Array
})

const professionColors = {
  Painter: 'rgba(252, 142, 74, 1)',
  Electrician: 'rgba(139, 179, 77, 1)',
  Mason: 'rgba(255, 238, 0, 1)',
  Plumber: 'rgba(116, 185, 205, 1)',
  Carpenter: 'rgba(180, 114, 190, 1)',
  default: 'rgba(233, 228, 222, 1)',
}

function getProfessionColor(profession) {
  return professionColors[profession]; 
}

function getActivityColor(activity) {
  return professionColors[activity] || professionColors.default
}

// Bookings need to blend together if they are of the same type. This function groups activity of the same type.
const groupedBookings = computed(() => {
   if (!props.dates) return []; // If no dates are sent return empty.

  return props.dates.map((week) => { // Go through each week (4 weeks in total).
    const groups = []; // The final list of groups.
    let currentGroup = []; // A list of days of same booking-type.

    week.dayDates.forEach((dayString, index) => { // dayDates is an array of dates as strings e.g. ("12/5").
      const [dayNumber, monthNumber] = dayString.split('/').map(Number); // Separate the values of the string.
      const date = new Date(2025, monthNumber - 1, dayNumber + 1); // Create a date-object of it, offset by -1 (january = 0) and +1 since we want to include the first day.

      const booking = props.employee.bookings.find(b => { // Checks if a booking is active for the date-period.
        const from = new Date(b.from);
        const to = new Date(b.to);
        return date >= from && date <= to; // Returns true if between the start- and end-date.
      })

      const groupKey = booking ? `${booking.status}-${booking.percentage}` : 'null'; // Gives each booking a key which is the status + percentage if there is one, else null.

      if (groupKey === 'null') { // If there is no booking. 
        if (currentGroup.length) {
          groups.push({ key: currentGroup[0].groupKey, days: [...currentGroup] }); // Adds the booking key + the days of the booking.
          currentGroup = []; // Initiates a new group
        }

        groups.push({ // Adds the no-booking day as a separate day since they will not be grouped with any other dates.
          key: 'null', 
          days: [{ booking: null, date, label: dayString, groupKey }] });
      } 
      else {  
        // If the current group isn't empty, we get the groupKey from a previous day of the same group, else null.
        const previousKey = currentGroup.length ? currentGroup[0].groupKey : null;

        // If we encounter a new booking-type the old one is added to the groups and a new group is initiated.
        if (groupKey !== previousKey && currentGroup.length) {
          groups.push({ key: previousKey, days: [...currentGroup] });
          currentGroup = [];
        }
        // Add the new groups first booking-day.
        currentGroup.push({ booking, date, label: dayString, groupKey });
      }
    })

    // Safe net to gather up remaining bookings at the end of the week e.g. if it has the same groupKey and therefor won't trigger a push since it's not a different key.
    if (currentGroup.length) {
      groups.push({ key: currentGroup[0].groupKey, days: [...currentGroup] });
    }

    return groups;
  })
})

// This function sets a unique style for each booking-type based on the previously defined groupKey
function getBlockStyle(group) {
  if (group.key === 'null') {
    return {
      margin: '0 2px',
      backgroundColor: 'rgba(172, 197, 165, 1)'
    }
  }

  const [status, percentage] = group.key.split('-')

  if (status === 'Absent') {
    return {
      backgroundColor: 'rgba(230, 72, 5, 0.65)',
      color: 'white',
      fontWeight: '600'
    }
  }

  else if (status === 'Booked') {
    if (+percentage === 100) {
      return { 
        backgroundColor: 'rgba(212, 232, 111, 1)',
        fontWeight: '600' 
      }
    }
    return {
      backgroundImage: 'repeating-linear-gradient(90deg, rgba(212, 232, 111, 1) 0, rgba(212, 232, 111, 1) 8px, white 8px, white 15px)',
      fontWeight: '600'
    }
  }

  else if (status === 'Preliminary') {
    if (+percentage === 100) {
      return { 
        backgroundColor: 'rgba(217, 217, 217, 1)',
        fontWeight: '600' 
      }
    }
    return {
      backgroundImage: 'repeating-linear-gradient(90deg, rgba(217, 217, 217, 1) 0, rgba(217, 217, 217, 1) 8px, white 8px, white 15px)',
      fontWeight: '600'
    }
  }

  return {}
}

// Prints booking-data for each booking-group
function getMessage(group) {
  const { booking } = group.days[0];
  if (!booking) return '';

  const { status, percentage } = booking
  if (status === 'Absent') return 'Frånvaro';
  if (status === 'Booked') return percentage === 100 ? '08:00-17:00<br>SKF' : '08:00-12:00<br>Volvo';
  if (status === 'Preliminary') return percentage === 100 ? '08:00-17:00<br>Volvo' : '08:00-12:00<br>SKF';

  return '';
}

</script>

<template>
  <div class="employee-row">
    <Employee :employee="employee"/>
    <div v-for="(week, index) in groupedBookings" 
      :key="index" 
      class="booked-week">
      <div 
        v-for="(group, g) in week" 
        :key="g" 
        class="booking-group"
        :class="{ 'free': group.key === 'null' }"
        :style="getBlockStyle(group)">
        <div 
          v-if="group.key !== 'null' && group.days[0].booking?.status !== 'Absent'" 
          class="bottom-color"
          :style="{ backgroundColor: getActivityColor(group.days[0].booking.activity) }">
        </div>
        <span v-if="group.key !== 'null'" class="booking-label" v-html="getMessage(group)"></span>
        <img v-if="group.key === 'null'" src="../assets/icons/book-icon.png" alt="book icon" class="book-icon">
      </div>

    </div>
  </div>
</template>

<style scoped>
.employee-row {
  display: flex;
  gap: 1rem;
  width: 100%;
  height: 100%;
  max-height: 65px;
  margin-bottom: .5rem;
}

.booked-week {
  flex: 1;
  display: flex;
  justify-content: space-between;
  background-color: white;
  max-width: 400px;
}

.booking-group {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.7rem;
  text-align: center;
  position: relative;
}

.booking-group.free {
  flex: 0 0 20%;
  margin: 0 4px;
  background-color: white;
  border: 1px solid #ccc;
  position: relative;
  max-width: 69px;
}

.booking-label {
  position: absolute;
  width: 100%;
  text-align: center;
}

.bottom-color {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 6px;
  width: 100%;
}
</style>
