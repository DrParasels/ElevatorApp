<template>
  <div class="container">
    <transition
      name="move-elevator"
      :style="{
        transform: `translateY(${-this.abc}px)`,
        transition: `transform ${this.timeItem}s linear`,
      }"
    >
      <div
        :class="{
          blinker: isActive,
        }"
        class="elevator"
      >
        <div v-if="show" class="current-floor">
          <span></span>
          {{ this.floor }}
        </div>
      </div>
    </transition>
    <div class="floor" ref="heig" v-for="(item, idx) of this.floors" :key="idx">
      <div class="lift-shaft"></div>
      <div class="hall">
        <div class="floor-num">{{ this.floors[idx] }}</div>
        <button
          @click="callfunc(item)"
          :style="styleObject(item)"
          class="call-btn"
        >
          <span :style="styleButton(item)"></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
// [x] 5. Отступ у лифта | Критичность: 4
// [x] 6. Мигание при простое | Критичность: 5
// [] 2. Смена стрелки | Критичность: 5
// [x] 4. :active | Критичность: 1
// [] 3. LoclStorage | Критичность: 4
// [] 8. разбитие на компоненты | Критичность: 5
// [] 1. название функций | Критичность: 2
// [x] 9. добавить дефолные знчения | Критичность: 3
// [x] 7. подумать над функцией settimeout async | Критичность: 2
// [x] 10. немного поменять диазйн | Критичность: 1
// [x] 10. определить из макета высоту | Критичность: 2
// [] подумать над флагами: критичность 3

export default {
  name: "App",
  data() {
    return {
      floors: [],
      floor: 1,
      selectedItem: [],
      abc: 0,
      timeItem: 0,
      elevatorHeight: 0,
      flag: false,
      show: false,
      isActive: false,
      floorsNumber: 0,
    };
  },
  DEFAULT_FLOORS: 5,
  TIME_ELEVATOR_WAITING: 3,

  components: {},
  created() {
    while (this.$options.DEFAULT_FLOORS > 0) {
      this.floors.push(this.$options.DEFAULT_FLOORS--);
    }
  },

  mounted() {
    this.elevatorHeight = this.$refs.heig[0].offsetHeight;
  },
  methods: {
    itemStyle() {
      return this.styleObject;
    },

    callfunc(item) {
      if (this.selectedItem.includes(item) || this.floor === item) {
        return;
      }
      this.selectedItem.push(item);
      if (this.flag) {
        return;
      }
      this.updateElevator();
    },

    async updateElevator() {
      // debugger;
      this.flag = true;
      this.show = true;
      this.timeItem = Math.abs(this.selectedItem[0] - this.floor);
      this.testFunc();
      await this.sleep(this.timeItem * 1000).then(() => {
        this.selectedItem.splice(0, 1);
        this.blinkfunc();
      });
      await this.sleep(this.$options.TIME_ELEVATOR_WAITING * 1000).then(() => {
        this.blinkfunc();
        this.show = false;
        if (this.selectedItem.length !== 0) {
          return this.updateElevator();
        }
        this.flag = false;
      });
    },
    testFunc() {
      this.abc += (this.selectedItem[0] - this.floor) * this.elevatorHeight;
      this.floor = this.selectedItem[0];
    },
    sleep(ms) {
      return new Promise((resolve) => {
        setTimeout(() => resolve(), ms);
      });
    },

    blinkfunc() {
      this.isActive = !this.isActive;
    },

    arrowDirection() {
      if (this.selectedItem[0] - this.floor < 0) {
        return { transform: "rotate(180deg)" };
      }
    },

    styleObject(item) {
      return {
        border: this.selectedItem.includes(item)
          ? "2px solid red"
          : "1px solid #000",
      };
    },
    styleButton(item) {
      return {
        backgroundColor: this.selectedItem.includes(item) ? "red" : "green",
      };
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

.floor {
  display: flex;
  border-bottom: 1px solid #000;
}

.lift-shaft {
  width: 4em;
  border-right: 1px solid #000;
  padding: 2.5em 0;
}

.hall {
  padding-left: 1em;
  padding-top: 1em;
}
.call-btn:active {
  background-color: aquamarine;
}

.call-btn {
  outline: none;
  border: 1px solid #000;
  background-color: transparent;
  padding: 1em;
  position: relative;
  cursor: pointer;
  transition: all 0.2s;
  border-radius: 1em;
}

.call-btn span {
  position: absolute;
  width: 0.8em;
  height: 0.8em;
  border-radius: 50%;
  background-color: green;
  top: 0.6em;
  left: 0.6em;
}
</style>
