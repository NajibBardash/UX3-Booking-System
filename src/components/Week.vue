<script setup>
import Day from './Day.vue';

defineProps({
    month: {
        type: String,
        required: true,
    },

    weekNumber: {
        type: String,
        required: true
    }
})

const dates = [
    {
        dayName: 'Mån',
        dayDate: '20/4'
    },
    {
        dayName: 'Tis',
        dayDate: '21/4'
    },
    {
        dayName: 'Ons',
        dayDate: '22/4'
    },
    {
        dayName: 'Tors',
        dayDate: '23/4'
    },
    {
        dayName: 'Fre',
        dayDate: '24/4'
    }
]

</script>

<template>
    <div class="week">
        <div class="month-week-wrapper">
            <span>{{ month }}</span>
            <span>{{ weekNumber }}</span>
        </div>
        <div class="day">
            <Day v-for="(date, index) in dates" 
                :key="index" 
                :day-name="date.dayName" 
                :day-date="date.dayDate">
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
    border: 1px solid red;

    .month-week-wrapper {
        flex: 1;
        display: flex;
        align-items: center;
        flex-direction: column;
        justify-content: end;
        height: 100%;
        border: 1px solid red;
    }

    .day {
        flex: 1;
        display: flex;
        padding: 0 1rem;
        justify-content: end;
        align-items: end;

        .date-wrapper {
            flex: 1;
            display: flex;
            flex-direction: column;
            text-align: center;
            border: 1px solid red;
        }
    }
}
</style>
