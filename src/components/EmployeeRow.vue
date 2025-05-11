<script setup>

defineProps({

})


</script>

<template>
<div class="employee-row">
        <div class="employee">
          <div class="vertical-colors">
            <div class="first-color"></div>
            <div class="second-color"></div>
          </div>
          <div class="name-wrapper">
            <img src="./assets/icons/info-icon.png" alt="info icon" class="info-icon">
            <p>Folke Andersson</p>
          </div>
        </div>
        <div class="booked-week">
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
        </div>
        <div class="booked-week">
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
        </div>
        <div class="booked-week">
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
        </div>
        <div class="booked-week">
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
          <div class="booked-day">apa</div>
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

.employee {
  flex: .645;
  display: flex;
  background-color: rgba(233, 228, 222, 1);
  border: 1px solid red;

  .vertical-colors {
    flex: .16;
    display: flex;
    width: 100%;
    padding-right: .5rem;

    .first-color {
      flex: 1;
      width: 100%;
      background-color: rgba(180, 114, 190, 1);
      border: 1px solid red;
    }

    .second-color {
      flex: 1;
      width: 100%;
      background-color: rgba(139, 179, 77, 1);
      border: 1px solid red;
    }
  }

  .name-wrapper {
    position: relative;
    top: -18px;

    p {
      max-width: 50px;
      font-size: 95%;
      font-weight: 500;
    }
  }
}

.booked-week {
  flex: 1;
  display: flex;
  justify-content: space-between;
  background-color: blanchedalmond;
  border: 1px solid red;
}

.booked-day {
  padding: 1rem .91rem;
  border: 1px solid red;
}
</style>
