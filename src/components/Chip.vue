<script setup>

defineProps({
  label: {
    type: String,
    required: true,
  },
})

defineEmits(['click'])

</script>

<template>
    <button
        class="chip poppins-regular"
        @click="$emit('click', $event)">
        <slot :label="label">
            {{ label }}
        </slot>        
    </button>
</template>

<style scoped>
    .chip {
        display: flex;
        background-color: white;
        padding: .4rem 1rem;
        border-radius: 20px;
        border-style: none;
        border: 2px solid rgb(114, 133, 109);
    }

    .chip.active {
        border: 2px solid black;
        background-color: #f0f0f0;
    }
</style>
