<template>
  <div class="container">
    <div
      class="elevator"
      :style="{
        transform: `translateY(${-heightToMove}px)`,
        transition: `transform ${timeToMove}s linear`,
      }"
      :class="{
        blinker: isActive,
      }"
    >
      <div v-if="flag" class="current-floor">
        <span :style="{ transform: `rotate(${rotateValue}deg)` }"></span>
        {{ elevatorPosition }}
      </div>
    </div>
    <floor-item
      ref="height"
      @select-floor="callfunc"
      :floor="floor"
      :calls-floors="callsFloors"
      v-for="floor of floors"
      :key="floor"
    />
  </div>
</template>

<script>
// [x] 5. Отступ у лифта | Критичность: 4
// [x] 6. Мигание при простое | Критичность: 5
// [+/-] 2. Смена стрелки | Критичность: 5
// [x] 4. :active | Критичность: 1
// [x] 3. LoclStorage | Критичность: 4
// [x] 8. разбитие на компоненты | Критичность: 5
// [x] 1. название функций | Критичность: 2
// [x] 9. добавить дефолные знчения | Критичность: 3
// [x] 7. подумать над функцией settimeout async | Критичность: 2
// [x] 10. немного поменять диазйн | Критичность: 1
// [x] 10. определить из макета высоту | Критичность: 2
// [+/-] подумать над флагами: критичность 3
import FloorItem from "@/components/FloorItem.vue";

export default {
  name: "App",

  components: {
    FloorItem,
  },

  data() {
    return {
      floors: [],
      callsFloors: [],
      heightToMove: 0,
      timeToMove: 0,
      elevatorPosition: 1,
      elevatorHeight: 0,
      flag: false,
      isActive: false,
      rotateValue: 0,
    };
  },
  DEFAULT_FLOORS: 5,
  TIME_ELEVATOR_WAITING: 3,
  created() {
    while (this.$options.DEFAULT_FLOORS > 0) {
      this.floors.push(this.$options.DEFAULT_FLOORS--);
    }
    this.elevatorPosition =
      JSON.parse(localStorage.getItem("currentFloor")) || 1;
    this.heightToMove = JSON.parse(localStorage.getItem("heightToMove")) || 0;
    this.callsFloors = JSON.parse(localStorage.getItem("selectedFloor")) || [];
    if (this.callsFloors.length > 0) {
      this.moveElevator();
    }
    this.$nextTick(() => {
      this.elevatorHeight = this.$refs.height[0].floorHeight;
    });
  },
  methods: {
    callfunc(floor) {
      if (this.callsFloors.includes(floor) || this.elevatorPosition === floor) {
        return;
      }
      this.callsFloors.push(floor);
      if (this.flag) {
        return;
      }
      this.moveElevator();
    },
    async moveElevator() {
      await new Promise((resolve) => {
        resolve();
      }).then(() => {
        this.flag = true;
        this.arrowDirection();
        this.timeHeightToMove();
      });

      await this.sleep(this.timeToMove * 1000).then(() => {
        this.callsFloors.shift();
        this.blinkfunc();
      });

      await this.sleep(this.$options.TIME_ELEVATOR_WAITING * 1000).then(() => {
        this.blinkfunc();
        this.show = false;
        if (this.callsFloors.length !== 0) {
          return this.moveElevator();
        }
        this.flag = false;
      });
    },
    sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), ms);
      });
    },
    timeHeightToMove() {
      this.timeToMove = Math.abs(this.callsFloors[0] - this.elevatorPosition);
      this.heightToMove +=
        (this.callsFloors[0] - this.elevatorPosition) * this.elevatorHeight;
      this.elevatorPosition = this.callsFloors[0];
    },

    blinkfunc() {
      this.isActive = !this.isActive;
    },
    arrowDirection() {
      console.log(this.elevatorPosition, this.callsFloors[0]);
      if (this.elevatorPosition - this.callsFloors[0] > 0) {
        console.log("2");
        this.rotateValue = 180;
        return;
      }
      this.rotateValue = 0;
    },
  },
  computed: {},
  watch: {
    elevatorPosition(newValue) {
      localStorage.setItem("currentFloor", JSON.stringify(newValue));
    },
    heightToMove(newValue) {
      localStorage.setItem("heightToMove", JSON.stringify(newValue));
    },
    callsFloors: {
      handler(newValue) {
        localStorage.setItem("selectedFloor", JSON.stringify(newValue));
      },
      deep: true,
    },
  },
};
</script>

<style>
#app {
  box-sizing: border-box;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 40px;
}

body {
  margin: 0;
}

.container {
  margin: 0 auto;
  max-width: 50em;
  border-top: 1px solid #000;
  border-left: 1px solid #000;
  border-right: 1px solid #000;
  font-size: 20px;
  background-color: #fff;
  position: relative;
}

.blinker {
  animation-name: blinker;
  animation-iteration-count: infinite;
  animation-timing-function: linear;
  animation-duration: 1s;
}

@keyframes blinker {
  50% {
    opacity: 0.4;
  }
}

.elevator {
  background-color: teal;
  width: 4em;
  height: 101px;
  position: absolute;
  bottom: 0;
  left: 0;
}

.current-floor span {
  margin-top: 3px;
  display: block;
  content: "";
  background-image: url("@/assets/up-arrow.png");
  background-size: contain;
  background-repeat: no-repeat;
  width: 10px;
  height: 10px;
}

.current-floor {
  padding-left: 0.5em;
  background-color: #fff;
  display: flex;
  margin-top: 10px;
  width: 30px;
  margin: 10px auto;
  font-size: 18px;
  border-radius: 3px;
}
</style>
