<template>
  <div class="container">
    <transition
      name="move-elevator"
      :style="{
        transform: `translateY(${-abc}em)`,
        transition: `all ${cba}s`,
      }"
    >
      <div class="elevator">
        <span class="current-floor">{{ this.selectedItem[1] }}</span>
      </div>
    </transition>
    <div
      class="floor"
      ref="height"
      v-for="(item, idx) of this.floors"
      :key="idx"
    >
      <div class="lift-shaft"></div>
      <div class="hall">
        <div class="floor-num">{{ this.floors[idx] }}</div>
        <button @click="call(item)" :style="styleObject(item)" class="call-btn">
          <span></span>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      floors: [5, 4, 3, 2, 1],
      floor: 1,
      selectedItem: [],
      abc: 0,
      cba: 0,
    };
  },
  components: {},
  mounted() {},
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
      setTimeout(async () => {
        while (this.selectedItem.length > 0) {
          await new Promise((resolve) => setTimeout(resolve, 5000));
          this.testFunc();
          this.afterTest();
        }
      }, 0);
    },

    testFunc() {
      this.abc += (this.selectedItem[0] - this.floor) * 5;
      this.cba = Math.abs(this.selectedItem[0] - this.floor);
    },

    afterTest() {
      this.floor = this.selectedItem[0];
      this.selectedItem.splice(0, 1);
    },

    styleObject(item) {
      return {
        backgroundColor: this.selectedItem.includes(item)
          ? "teal"
          : "transparent",
      };
    },
  },
  computed: {
    floorHeight() {
      return this.$refs.height.clientHeight;
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
  border: 1px solid #000;
  font-size: 20px;
  background-color: #fff;
  position: relative;
}

.elevator {
  background-color: teal;
  width: 4em;
  height: 5em;
  position: absolute;
  bottom: 0;
  left: 0;
}

.current-floor {
  background-color: #fff;
  display: block;
  margin-top: 10px;
  width: 30px;
  margin: 10px auto;
  font-size: 18px;
  border-radius: 3px;
}

.floor {
  display: flex;
}

.floor:not(:last-child) {
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

.call-btn {
  outline: none;
  border: 1px solid #000;
  background-color: transparent;
  padding: 1em;
  position: relative;
  cursor: pointer;
  transition: all 0.5s;
}

.call-btn span {
  position: absolute;
  width: 0.8em;
  height: 0.8em;
  border-radius: 50%;
  background-color: red;
  top: 0.6em;
  left: 0.6em;
}
</style>
