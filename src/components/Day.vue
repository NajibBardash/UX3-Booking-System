<script setup>

defineProps({
    dayName: {
        type: String,
        required: true,
    },

    dayDate: {
        type: String,
        required: true
    }
})

// Activates a day (changes color) by clicking on it
function activateDay(evt) {
    evt.currentTarget.classList.toggle('active');
}

</script>

<template>
    <div class="date-wrapper"
        @click="activateDay">
        <span>{{ dayName }}</span>
        <span>{{ dayDate }}</span>
    </div>
</template>

<style scoped>

.date-wrapper {
    flex: 1;
    display: flex;
    flex-direction: column;
    text-align: center;
    border-radius: 5px;
}

.date-wrapper:hover {
    cursor: pointer;
}

.active {
    background-color: rgba(221, 120, 58, 1);
}

</style>
