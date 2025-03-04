<template>
  <div id="app">
    <h1>타이머: {{ timerDisplay }}초 | 스톱워치: {{ stopwatchDisplay }}</h1>
    <div class="timer-input">
      <input
        type="number"
        v-model.number="timerInput"
        placeholder="타이머 시간(초)"
        :disabled="timerRunning"
        min="1"
      />
      <button @click="startTimer" :disabled="timerRunning || timerInput <= 0">
        타이머 시작
      </button>
      <button @click="pauseTimer" :disabled="!timerRunning">일시정지</button>
      <button @click="resumeTimer" :disabled="timerRunning || timerTime <= 0">
        재개
      </button>
      <button @click="resetTimer" :disabled="timerRunning">초기화</button>
    </div>
    <div class="controls">
      <button @click="startStopwatch" :disabled="stopwatchRunning">
        스톱워치 시작
      </button>
      <button @click="recordStopwatch" :disabled="!stopwatchRunning">
        기록
      </button>
      <button @click="stopStopwatch" :disabled="!stopwatchRunning">
        스톱워치 멈춤
      </button>
      <button @click="resetStopwatch">스톱워치 초기화</button>
    </div>
    <div class="records">
      <h2>스톱워치 기록</h2>
      <ul>
        <li v-for="(record, index) in stopwatchRecords" :key="index">
          {{ record }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      timerInput: "", // 사용자가 입력한 초기 시간
      timerTime: 0, // 실제 타이머 카운트다운 시간
      timerRunning: false,
      timerId: null,
      stopwatchTime: 0, // 스톱워치 시간 (100분의 1초 단위)
      stopwatchRunning: false,
      stopwatchId: null,
      stopwatchRecords: [],
    };
  },
  computed: {
    timerDisplay() {
      return this.timerRunning || this.timerTime > 0
        ? this.timerTime
        : this.timerInput || 0;
    },
    stopwatchDisplay() {
      const totalSeconds = this.stopwatchTime / 100; // 100분의 1초를 초 단위로 변환
      const minutes = Math.floor(totalSeconds / 60);
      const seconds = Math.floor(totalSeconds % 60);
      const centiseconds = Math.floor((totalSeconds * 100) % 100); // 100분의 1초
      return `${minutes.toString().padStart(2, "0")}:${seconds
        .toString()
        .padStart(2, "0")}.${centiseconds.toString().padStart(2, "0")}`;
    },
  },
  methods: {
    startTimer() {
      if (!this.timerRunning && this.timerInput > 0) {
        this.timerTime = this.timerInput;
        this.timerRunning = true;
        this.runTimer();
      }
    },
    runTimer() {
      this.timerId = setInterval(() => {
        if (this.timerTime > 0) {
          this.timerTime--;
        } else {
          clearInterval(this.timerId);
          this.timerRunning = false;
          alert("타이머가 끝났습니다!");
        }
      }, 1000);
    },
    pauseTimer() {
      if (this.timerRunning) {
        clearInterval(this.timerId);
        this.timerRunning = false;
      }
    },
    resumeTimer() {
      if (!this.timerRunning && this.timerTime > 0) {
        this.timerRunning = true;
        this.runTimer();
      }
    },
    resetTimer() {
      clearInterval(this.timerId);
      this.timerRunning = false;
      this.timerTime = this.timerInput || 0;
    },
    startStopwatch() {
      if (!this.stopwatchRunning) {
        this.stopwatchRunning = true;
        this.stopwatchId = setInterval(() => {
          this.stopwatchTime += 1; // 10ms마다 1 증가 (100분의 1초 단위)
        }, 10); // 10ms 간격
      }
    },
    recordStopwatch() {
      if (this.stopwatchRunning) {
        this.stopwatchRecords.push(this.stopwatchDisplay); // 현재 시간 기록
      }
    },
    stopStopwatch() {
      if (this.stopwatchRunning) {
        clearInterval(this.stopwatchId);
        this.stopwatchRunning = false;
        this.stopwatchRecords.push(this.stopwatchDisplay); // 멈출 때도 기록
      }
    },
    resetStopwatch() {
      clearInterval(this.stopwatchId);
      this.stopwatchRunning = false;
      this.stopwatchTime = 0;
      this.stopwatchRecords = []; // 기록도 초기화
    },
  },
  beforeUnmount() {
    if (this.timerId) clearInterval(this.timerId);
    if (this.stopwatchId) clearInterval(this.stopwatchId);
  },
};
</script>

<style>
#app {
  text-align: center;
  margin: 20px auto;
  font-family: Arial, sans-serif;
  max-width: 400px;
  padding: 10px;
}

h1 {
  color: #333;
  font-size: 20px;
  word-break: keep-all;
}

.timer-input,
.controls {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 10px;
  margin: 10px 0;
}

.timer-input input {
  padding: 8px;
  font-size: 14px;
  width: 100%;
  max-width: 150px;
  border: 1px solid #ccc;
  border-radius: 5px;
  text-align: center;
}

button {
  padding: 8px 12px;
  font-size: 14px;
  border: none;
  border-radius: 5px;
  background-color: #4caf50;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
  flex: 1 1 45%;
  min-width: 100px;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #45a049;
}

.records ul {
  list-style: none;
  padding: 0;
  margin: 0 auto;
  max-width: 200px;
}

.records li {
  background-color: #f9f9f9;
  padding: 8px;
  margin: 5px 0;
  border-radius: 5px;
  text-align: center;
  font-size: 14px;
}

@media (max-width: 400px) {
  button {
    font-size: 12px;
    padding: 6px;
  }
}
</style>
