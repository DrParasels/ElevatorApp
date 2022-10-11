<template>
  <div class="container">
    <transition
      name="move-elevator"
      :style="{
        transform: `translateY(${-abc}px)`,
        transition: `transform ${timeItem}s linear`,
      }"
    >
      <div class="elevator">
        <span class="current-floor">{{ this.selectedItem[0] }}</span>
      </div>
    </transition>
    <div class="floor" ref="heig" v-for="(item, idx) of this.floors" :key="idx">
      <div class="lift-shaft"></div>
      <div class="hall">
        <div class="floor-num">{{ this.floors[idx] }}</div>
        <button @click="call(item)" :style="styleObject(item)" class="call-btn">
          <span :style="styleButton(item)"></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
// [] 5. Отступ у лифта | Критичность: 4
// [] 6. Мигание при простое | Критичность: 5
// [] 2. Смена стрелки | Критичность: 5
// [x] 4. :active | Критичность: 1
// [] 3. LoclStorage | Критичность: 4
// [] 8. разбитие на компоненты | Критичность: 5
// [] 1. название функций | Критичность: 3
// [] 9. добавить дефолные знчения | Критичность: 3
// [] 7. подумать над функцией settimeout async | Критичность: 2
// [x] 10. немного поменять диазйн | Критичность: 1
// [] 10. определить из макета высоту | Критичность: 2

export default {
  name: "App",
  data() {
    return {
      floors: [5, 4, 3, 2, 1],
      floor: 1,
      selectedItem: [],
      abc: 0,
      timeItem: 0,
      elevatorHeight: 0,
    };
  },
  components: {},
  mounted() {
    this.elevatorHeight = this.$refs.heig[0].offsetHeight;
  },
  methods: {
    itemStyle() {
      return this.styleObject;
    },
    show() {
      console.log(this.floorHeight);
    },
    call(item) {
      if (this.selectedItem.includes(item) || this.floor === item) {
        return;
      }
      this.selectedItem.push(item);

      if (this.selectedItem.length > 1) {
        return;
      }
      this.updateElevator();
    },

    async updateElevator() {
      const sleep = (ms) => {
        return new Promise((resolve) => {
          setTimeout(() => resolve(), ms);
        });
      };
      this.timeItem = Math.abs(this.selectedItem[0] - this.floor);
      this.testFunc();
      await sleep(this.timeItem * 1000).then(() => {});
      console.log(this.selectedItem.shift());
      await sleep(3000).then(() => {
        if (this.selectedItem.length !== 0) {
          return this.updateElevator();
        }
      });

      // this.timeItem = Math.abs(this.selectedItem[0] - this.floor);
      // this.testFunc();

      // await new Promise((resolve) =>
      //   setTimeout(resolve, this.timeItem * 1000)
      // );

      // await new Promise((resolve) => setTimeout(resolve, 3000));
      // this.selectedItem.splice(0, 1);
    },

    testFunc() {
      this.abc += (this.selectedItem[0] - this.floor) * this.elevatorHeight;
      this.floor = this.selectedItem[0];
    },

    afterTest() {
      this.selectedItem.splice(0, 1);
      console.log(this.selectedItem, this.floor);
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
  computed: {},
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

.elevator {
  background-color: teal;
  width: 4em;
  height: 101px;
  position: absolute;
  bottom: 0;
  left: 0;
}

.current-floor::before {
  content: "";
  position: absolute;
  left: 0.3em;
  bottom: 0.35em;
  background-image: url("@/assets/up-arrow.png");
  background-size: contain;
  background-repeat: no-repeat;
  width: 10px;
  height: 10px;
}

.current-floor {
  padding-left: 0.5em;
  background-color: #fff;
  display: block;
  margin-top: 10px;
  width: 30px;
  margin: 10px auto;
  font-size: 18px;
  border-radius: 3px;
  position: relative;
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
