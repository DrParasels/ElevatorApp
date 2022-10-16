<template>
  <div class="container">
    <div
      v-for="item in items"
      :key="item"
      class="elevator"
      :style="{
        transform: `translateY(${-item.heightToMove}px)`,
        transition: `transform ${item.timeToMove}s linear`,
        left: `${item.id * 81 - 81}px`,
      }"
      :class="{
        blinker: item.isActive,
      }"
    >
      <div v-if="item.flag" class="current-floor">
        <span :style="{ transform: `rotate(${item.rotateValue}deg)` }"></span>
        {{ item.elevatorPosition }}
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

      currentEle: 1,
      items: [
        {
          id: 1,
          heightToMove: 0,
          timeToMove: 0,
          elevatorPosition: 1,
          callsArr: [],
          flag: false,
          isActive: false,
          rotateValue: 0,
        },
        {
          id: 2,
          heightToMove: 0,
          timeToMove: 0,
          elevatorPosition: 1,
          callsArr: [],
          flag: false,
          isActive: false,
          rotateValue: 0,
        },
        {
          id: 3,
          heightToMove: 0,
          timeToMove: 0,
          elevatorPosition: 1,
          callsArr: [],
          flag: false,
          isActive: false,
          rotateValue: 0,
        },
      ],
    };
  },
  DEFAULT_FLOORS: 5,
  TIME_ELEVATOR_WAITING: 3,
  created() {
    while (this.$options.DEFAULT_FLOORS > 0) {
      this.floors.push(this.$options.DEFAULT_FLOORS--);
    }
    // this.elevatorPosition =
    //   JSON.parse(localStorage.getItem("currentFloor")) || 1;
    // this.heightToMove = JSON.parse(localStorage.getItem("heightToMove")) || 0;
    // this.callsFloors = JSON.parse(localStorage.getItem("selectedFloor")) || [];
    if (this.callsFloors.length > 0) {
      this.moveElevator();
    }
    this.$nextTick(() => {
      this.elevatorHeight = this.$refs.height[0].floorHeight;
    });
  },
  computed: {},
  methods: {
    //функция которая определяет какой лифт вызывать
    findTime(arr, position, floor) {
      let time = 0;
      for (let i = 0; i < arr.length - 1; i++) {
        time += Math.abs(arr[i] - arr[i + 1]);
      }
      console.log(time + arr.length * 3 + Math.abs(position - floor), "2222");
      return time + arr.length * 3 + Math.abs(position - floor);
    },
    elevatorForCall(floor) {
      let ele = 0;
      for (let i = 1; i < this.items.length; i++) {
        if (
          this.findTime(
            this.items[i].callsArr,
            this.items[i].elevatorPosition,
            floor
          ) <
          this.findTime(
            this.items[ele].callsArr,
            this.items[ele].elevatorPosition,
            floor
          )
        ) {
          ele = i;
        }
      }
      console.log(ele, "2323232");
      return ele;
    },
    callfunc(floor) {
      if (
        this.items.find((i) => i.callsArr.includes(floor)) ||
        this.items.find((i) => i.elevatorPosition === floor)
      ) {
        console.log("1");
        return;
      }

      this.callsFloors.push(floor);
      this.currentElev = this.elevatorForCall(floor);

      this.items[this.currentElev].callsArr.push(floor);

      if (this.items[this.currentElev].callsArr.length > 1) {
        return;
      }

      this.moveElevator(this.items[this.currentElev]);
      // console.log("1");
    },
    async moveElevator(item) {
      // console.log(item);
      await new Promise((resolve) => {
        resolve();
      }).then(() => {
        item.flag = true;
        item.timeToMove = Math.abs(item.callsArr[0] - item.elevatorPosition);
        item.heightToMove +=
          (item.callsArr[0] - item.elevatorPosition) * this.elevatorHeight;
        item.elevatorPosition = item.callsArr[0];
      });

      await this.sleep(item.timeToMove * 1000).then(() => {
        this.blinkfunc(item);
      });

      await this.sleep(this.$options.TIME_ELEVATOR_WAITING * 1000).then(() => {
        this.blinkfunc(item);
        this.callsFloors.shift();
        item.callsArr.shift();
        if (item.callsArr.length !== 0) {
          return this.moveElevator(item);
        }
        item.flag = false;
      });
    },
    sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), ms);
      });
    },
    blinkfunc(item) {
      item.isActive = !item.isActive;
    },
    arrowDirection(item) {
      if (item.elevatorPosition - item.callsFloors[0] > 0) {
        item.rotateValue = 180;
        return;
      }
      item.rotateValue = 0;
    },
  },

  watch: {
    // elevatorPosition(newValue) {
    //   localStorage.setItem("currentFloor", JSON.stringify(newValue));
    // },
    // heightToMove(newValue) {
    //   localStorage.setItem("heightToMove", JSON.stringify(newValue));
    // },
    // callsFloors: {
    //   handler(newValue) {
    //     localStorage.setItem("selectedFloor", JSON.stringify(newValue));
    //   },
    //   deep: true,
    // },
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
