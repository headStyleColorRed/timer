<template>
    <div id="app">
      <div v-if="countdownTime" class="countdown-container">
        <p class="title">😏</p>
        <p class="countdown">{{ formatTime(countdownTime.days) }} days</p>
        <p class="countdown">{{ formatTime(countdownTime.hours) }} hours</p>
        <p class="countdown">{{ formatTime(countdownTime.minutes) }} minutes</p>
        <p class="countdown">{{ formatTime(countdownTime.seconds) }} seconds</p>
      </div>
      <div v-else>
        <p class="expired-message">Countdown expired!</p>
      </div>
    </div>
  </template>

  <script>
  import { ref, watch, onMounted } from 'vue';
  import { format, differenceInSeconds } from 'date-fns';

  export default {
    name: 'App',
    setup() {
      const targetDate = new Date('2024-01-18T18:55:00');
      const currentTime = ref(new Date());

      const calculateCountdownTime = (current, target) => {
        const diffInSeconds = differenceInSeconds(target, current);
        const days = Math.floor(diffInSeconds / 86400);
        const hours = Math.floor((diffInSeconds % 86400) / 3600);
        const minutes = Math.floor((diffInSeconds % 3600) / 60);
        const seconds = diffInSeconds % 60;

        return { days, hours, minutes, seconds };
      };

      const countdownTime = ref(calculateCountdownTime(currentTime.value, targetDate));

      const formatTime = (value) => {
        return value < 10 ? `0${value}` : value;
      };

      const updateCountdown = () => {
        countdownTime.value = calculateCountdownTime(currentTime.value, targetDate);
      };

      onMounted(() => {
        const timerId = setInterval(() => {
          currentTime.value = new Date();
          updateCountdown();
        }, 1000);

        watch(() => currentTime.value, updateCountdown);

        return () => clearInterval(timerId);
      });

      return { countdownTime, formatTime };
    },
  };
  </script>

  <style scoped>
  #app {
    position: absolute;
    top: 50%;
    left: 53%;
    transform: translate(-50%, -50%);
  }

    .title {
        font-size: 2em;
        margin: 10px;
        color: whitesmoke;
    }

  .countdown-container {
    text-align: center;
    width: 30rem;
  }

  .countdown {
    font-size: 1.5em;
    margin: 10px;
  }

  .expired-message {
    font-size: 2em;
    color: red;
  }
  </style>
