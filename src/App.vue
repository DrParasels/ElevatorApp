<template>
  <div class="container">
    <h1 class="title">Move Elevator App</h1>
    <input-item @makefloors-elevators="makeFloorsElevators" />
    <div class="building">
      <div
        v-for="item in items"
        :key="item"
        class="elevator"
        :style="{
          transform: `translateY(${-heightToMove[item]}px)`,
          transition: `transform ${timeToMove[item]}s linear`,
          left: `${item * 81}px`,
        }"
        :class="{
          blinker: isActive[item],
        }"
      >
        <div v-if="flag[item]" class="current-floor">
          <span
            :style="{ transform: `rotate(${rotateValue[item]}deg)` }"
          ></span>
          {{ elevatorPosition[item] }}
        </div>
      </div>
      <floor-item
        ref="height"
        @select-floor="callfunc"
        :floor="floor"
        :items="items"
        :calls-arr="callsArr"
        v-for="floor of floors"
        :key="floor"
      />
    </div>
  </div>
</template>

<script>
import FloorItem from "@/components/FloorItem.vue";
import InputItem from "@/components/InputItem.vue";

export default {
  name: "App",

  components: {
    FloorItem,
    InputItem,
  },

  data() {
    return {
      floors: [5, 4, 3, 2, 1],
      callsFloors: [[], [], []],
      elevatorHeight: 0,

      heightToMove: [0, 0, 0],
      timeToMove: [0, 0, 0],
      elevatorPosition: [1, 1, 1],
      callsArr: [[], [], []],
      flag: [false, false, false],
      isActive: [false, false, false],
      rotateValue: [0, 0, 0],
      currentEle: 1,
      items: [0, 1, 2],
    };
  },
  TIME_ELEVATOR_WAITING: 3,
  created() {
    // this.$nextTick(() => {
    //   this.elevatorHeight = this.$refs;
    // });
    // console.log(this.$refs.height);
    // console.log(this.items);
    // this.elevatorPosition =
    //   JSON.parse(localStorage.getItem("currentFloor")) || [];
    // this.heightToMove = JSON.parse(localStorage.getItem("heightToMove"));
    // this.callsArr = JSON.parse(localStorage.getItem("selectedFloor")) || [];
    // this.callsArr.forEach((element, index) => {
    //   if (element.length > 0) {
    //     this.moveElevator(index);
    //   }
    // });
  },
  computed: {},
  methods: {
    makeFloorsElevators(elevators, floors) {
      this.clearData();
      while (floors > 0) {
        this.floors.push(floors--);
      }
      while (elevators > 0) {
        this.items.push(--elevators);
        this.heightToMove.push(0);
        this.timeToMove.push(0);
        this.elevatorPosition.push(1);
        this.callsArr.push([]);
        this.flag.push(false);
        this.isActive.push(false);
        this.rotateValue.push(0);
      }
      this.items = this.items.reverse();
      this.$nextTick(() => {
        this.elevatorHeight = this.$refs.height[0].floorHeight;
      });
    },
    //функция которая определяет какой лифт вызывать
    findTime(arr, position, floor) {
      let time = 0;
      for (let i = 0; i < arr.length - 1; i++) {
        time += Math.abs(arr[i] - arr[i + 1]);
        console.log(time);
      }
      return time + arr.length * 3 + Math.abs(position - floor);
    },
    elevatorForCall(floor) {
      let ele = 0;
      // console.log(this.items[0], this.items[1], this.items[2]);
      for (let i = 1; i < this.items.length; i++) {
        if (
          this.findTime(this.callsArr[i], this.elevatorPosition[i], floor) <
          this.findTime(this.callsArr[ele], this.elevatorPosition[ele], floor)
        ) {
          ele = i;
        }
      }
      return ele;
    },
    callfunc(floor, eleheight) {
      this.elevatorHeight = eleheight;
      if (
        this.elevatorPosition.includes(floor) ||
        this.callsArr.find((i) => i.includes(floor))
      ) {
        console.log("2");
        return;
      }
      this.currentElev = this.elevatorForCall(floor);
      this.callsArr[this.currentElev].push(floor);
      if (this.callsArr[this.currentElev].length > 1) {
        return;
      }

      this.moveElevator(this.currentElev);
      // console.log("1");
    },
    async moveElevator(item) {
      // console.log(item);
      await new Promise((resolve) => {
        resolve();
      }).then(() => {
        this.flag[item] = true;
        this.arrowDirection(item);
        this.timeToMove[item] = Math.abs(
          this.callsArr[item][0] - this.elevatorPosition[item]
        );
        this.heightToMove[item] +=
          (this.callsArr[item][0] - this.elevatorPosition[item]) *
          this.elevatorHeight;
        this.elevatorPosition[item] = this.callsArr[item][0];
      });

      await this.sleep(this.timeToMove[item] * 1000).then(() => {
        this.blinkfunc(item);
      });

      await this.sleep(this.$options.TIME_ELEVATOR_WAITING * 1000).then(() => {
        this.blinkfunc(item);
        this.callsArr[item].shift();

        if (this.callsArr[item].length !== 0) {
          return this.moveElevator(item);
        }
        this.flag[item] = false;
      });
    },
    sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), ms);
      });
    },
    blinkfunc(item) {
      this.isActive[item] = !this.isActive[item];
    },
    clearData() {
      this.floors = [];
      this.heightToMove = [];
      this.timeToMove = [];
      this.elevatorPosition = [];
      this.callsArr = [];
      this.flag = [];
      this.isActive = [];
      this.rotateValue = [];
      this.currentEle = 1;
      this.items = [];
    },
    arrowDirection(item) {
      if (this.elevatorPosition[item] - this.callsArr[item][0] > 0) {
        this.rotateValue[item] = 180;
        return;
      }
      this.rotateValue[item] = 0;
    },
    renderFloorsAndElevators() {},
  },

  watch: {
    elevatorPosition: {
      handler(newValue) {
        localStorage.setItem("currentFloor", JSON.stringify(newValue));
      },
      deep: true,
    },
    heightToMove: {
      handler(newValue) {
        localStorage.setItem("heightToMove", JSON.stringify(newValue));
      },
      deep: true,
    },
    callsArr: {
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
  padding: 0 20px;
  max-width: 70em;
}

.building {
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
