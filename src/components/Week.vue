<script setup>
import Day from './Day.vue';

defineProps({
    dates: Object,
    isActive: Boolean
})

defineEmits(['click'])

</script>

<template>
    <div 
        class="week"
        :class="{ active: isActive }"
        @click="$emit('click')">
        <div class="month-week-wrapper">
            <span>{{ dates.month }}</span>
            <span>{{ dates.weekNumber }}</span>
        </div>
        <div class="day">
            <Day v-for="(dayName, index) in dates.dayNames" 
                :key="index" 
                :day-name="dayName" 
                :day-date="dates.dayDates[index]">
            </Day>
        </div>
    </div>
</template>

<style scoped>
.week {
    flex: 1;
    display: flex;
    justify-content: space-between;
    flex-direction: column;
    height: 100%;
    border-top-left-radius: 30px;
    border-top-right-radius: 30px;
    font-weight: 600;

    .month-week-wrapper {
        flex: 1;
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: end;
        height: 100%;
    }

    .day {
        flex: 1;
        display: flex;
        padding: 0 1rem;
        justify-content: end;
        align-items: end;
        gap: .2rem;
        padding-bottom: .5rem;
    }
}

.week:hover {
    cursor: pointer;
}

.week.active {
  background-color: white;
  color: black;
}
</style>
