<script setup>
import { ref } from 'vue';
import { computed } from 'vue';
import infoIcon from '../assets/icons/info-icon.png';
import activeInfoIcon from '../assets/icons/active-info-icon.png';

const { employee } = defineProps({
    employee: Object,
});

const professionColors = {
  Painter: 'rgba(252, 142, 74, 1)',
  Electrician: 'rgba(139, 179, 77, 1)',
  Mason: 'rgba(255, 238, 0, 1)',
  Plumber: 'rgba(116, 185, 205, 1)',
  Carpenter: 'rgba(180, 114, 190, 1)',
  default: 'rgba(233, 228, 222, 1)',
}

const professionMap = {
  Painter: 'Målare',
  Electrician: 'Elektriker',
  Mason: 'Murare',
  Plumber: 'Rörmockare',
  Carpenter: 'Snickare',
};

function getProfessionColor(profession) {
  return professionColors[profession]; 
}

const tooltipVisible = ref(false);

function toggleTooltip() {
  tooltipVisible.value = !tooltipVisible.value;
}

function normalize(str) {
  return str
    .toLowerCase()
    .replace(/å/g, 'a')
    .replace(/ä/g, 'ae')
    .replace(/ö/g, 'o')
    .replace(/[^a-z]/g, '');
}

const email = computed(() => {
  const parts = employee.name.split(' ');
  if (parts.length < 2) return '';
  const [first, last] = parts;
  return `${normalize(first)}.${normalize(last)}@sh.se`;
});

const translatedProfessions = computed(() => {
  return employee.professions
    .map((p) => professionMap[p] || p)
    .join(' / ');
});

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
        <img 
            :src="tooltipVisible ? activeInfoIcon : infoIcon" 
            alt="info icon" 
            class="info-icon" 
            @click="toggleTooltip">
        <div v-if="tooltipVisible" class="tooltip">
            <h4>{{ employee.name }}</h4>
            <span class="professions">{{ translatedProfessions }}</span><br>
            <div class="telephone-email">
                <span class="telephone">0704-233997</span><br>
                <span class="email">{{ email }}</span><br>
            </div>
            <button class="tooltip-button">Skicka direktmeddelande</button>
        </div>
        <p>{{ employee.name }}</p>
      </div>
    </div>
</template>

<style scoped>

.employee {
  flex: .645;
  display: flex;
  min-width: 210px;
  max-width: 210px;
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

.tooltip {
    position: absolute;
    top: -9.5rem;
    left: 2rem;
    background-color: white;
    padding: 1.5rem 1rem 1.5rem 1.5rem;
    border-radius: 5px;
    box-shadow: 10px 10px 20px rgba(0,0,0,0.65);
    z-index: 100;
    min-width: 200px;
    width: 240px;
    height: 190px;

    h4 {
        padding-bottom: 0.2rem;
        line-height: .7;
    }

    .telephone-email {
        padding-top: 1rem;
        font-size: 90%;
    }

}

.tooltip-button {
   background-color: rgba(103, 114, 100, 1);
   color: white;
   border-style: none;
   border-radius: 20px;
   border: 1px solid white;
   margin-top: 1rem;
   padding: .5rem .75rem;
   font-size: 90%;
   font-weight: 500;
}

.tooltip-button:hover {
    cursor: pointer;
    border: 1px solid black;
}

.tooltip-button:active {
    background-color: rgba(123, 124, 120, 1);
}
</style>
