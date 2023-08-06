<template>
  <div>
    <h1>Вращение</h1>
    <img src="~/assets/spin.webp" alt="spin" style="transform: rotateY(180deg); max-width: 100px;">
    <table>
      <tbody>
        <tr><td>Текущее угловое ускорение: {{ rotationRateY?.toFixed(0) }} град./с</td></tr>
        <tr><td>Макс. зафиксированное: {{ maxRotationRateY?.toFixed(0) }} град./с</td></tr>
        <tr><td>Или: <strong>{{ (maxRotationRateY * 0.167).toFixed(0) }} об./мин</strong></td></tr>
      </tbody>
    </table>
    <details>
      <summary>Примеры</summary>
      <table style="font-size: 14px;">
        <tbody>
          <tr><td>Карусель на детской площадке: 3-5 об./м</td></tr>
          <tr><td>Барабан в бетономешалке: 15-25 об./м</td></tr>
          <tr><td>Лента магнитофона: 100-150 об./м</td></tr>
          <tr><td>Потолочный вентилятор: 200-300 об./м</td></tr>
          <tr><td>Пропеллер самолета: 400-500 об./м</td></tr>
          <tr><td>Вентилятор кондиционера: 700-1500 об./м</td></tr>
        </tbody>
      </table>
    </details>
    <h1>Свободное падение</h1>
    <img src="~/assets/падение.webp" alt="spin" style="transform: rotateY(180deg); max-width: 100px;">
    <table>
      <tbody>
        <tr><td>Текущее ускорение: {{ accelerationZ?.toFixed(0) }} м/с²</td></tr>
        <tr><td>Макс. зафиксированное: {{ maxAccelerationZ?.toFixed(0) }} м/с²</td></tr>
        <tr><td>Макс. время свободного падения: {{ dropTime }} мс</td></tr>
        <tr><td>Макс. расстояние падения: {{ dropDistance.toFixed(2) }} м</td></tr>
      </tbody>
    </table>
    <details>
      <summary>Как получить корректные значения:</summary>
      <p style="font-size: 14px;">
        1. Никак <br>
        2. Расположите телефон параллельно земле (как на картинке) <br>
        3. Убедитесь что он находится в состоянии покоя <br>
        4. Нажмите кнопку "Сброс" и отпустите <br>
        5. Молитесь чтобы датчик отработал как положено <br>
        6. Расстройтесь из-за того что этого не произошло и вернитесь к 2 пункту
      </p>
    </details>
    <button @click="maxRotationRateY = 0; maxAccelerationZ = 0; dropTime = 0" style="padding: 4px 28px;">Сброс</button>
  </div>
</template>

<script setup>

let rotationRateY = ref(0);
let accelerationZ = ref(0);
let maxRotationRateY = ref(0);
let maxAccelerationZ = ref(0);
let dropTime = ref(0);
let startTime = 0;

function deviceMotion() {
  window.addEventListener('devicemotion', (e) => {
    rotationRateY.value = e.rotationRate.gamma;
    accelerationZ.value = e.acceleration.z;
    maxRotationRateY.value = Math.max(rotationRateY.value, maxRotationRateY.value);
    maxAccelerationZ.value = Math.max(accelerationZ.value, maxAccelerationZ.value);
    dropTimeComp(e.acceleration.z);
  });
}

function dropTimeComp(dropAccel) {
  if (dropAccel > 0 && !startTime) {
    startTime = new Date();
  } else if (dropAccel < 0 && startTime) {
    const result = new Date() - startTime;
    if (result > dropTime.value) {
      dropTime.value = result;
    }
    startTime = 0;
  }
}

const dropDistance = computed(() => {
  return 0.5 * 9.81 * ((dropTime.value / 1000) ** 2);
});

onMounted(() => {
  deviceMotion();
});

</script>
