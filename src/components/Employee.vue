<script setup>

defineProps({
    employee: Object,
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

</script>

<template>
    <div class="employee">
      <div class="vertical-colors">
        <div class="first-color" :style="{ backgroundColor: getProfessionColor(employee.professions[0]) }"></div>
        <div class="second-color" :style="{ backgroundColor: getProfessionColor(employee.professions[1] && employee.professions[1] !== employee.professions[0] 
          ? employee.professions[1] : 'default' )}">
        </div>
      </div>
      <div class="name-wrapper">
        <img src="../assets/icons/info-icon.png" alt="info icon" class="info-icon">
        <p>{{ employee.name }}</p>
      </div>
    </div>
</template>

<style scoped>

.employee {
  flex: .645;
  display: flex;
  background-color: rgba(233, 228, 222, 1);

  .vertical-colors {
    flex: .16;
    display: flex;
    width: 100%;
    padding-right: .5rem;

    .first-color {
      flex: 1;
      width: 100%;
      background-color: rgba(180, 114, 190, 1);
    }

    .second-color {
      flex: 1;
      width: 100%;
      background-color: rgba(139, 179, 77, 1);
    }
  }

  .name-wrapper {
    position: relative;
    top: -18px;

    p {
      max-width: 70px;
      font-size: 95%;
      font-weight: 500;
    }
  }
}
</style>
