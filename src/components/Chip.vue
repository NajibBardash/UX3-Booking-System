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
    }

    .chip:hover {
        cursor: pointer;
    }
</style>
