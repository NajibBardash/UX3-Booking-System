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

</script>

<template>
    <div class="date-wrapper">
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
    border: 1px solid red;
}

</style>
