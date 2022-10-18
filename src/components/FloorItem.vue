<template>
  <div ref="height" class="floor">
    <div
      v-for="numbersOfelevator in items"
      :key="numbersOfelevator"
      class="lift-shaft"
    ></div>
    <div class="hall">
      <div class="floor-num">{{ floor }}</div>
      <button
        @click="callfunc(floor)"
        :style="styleButtonBorder(floor)"
        class="call-btn"
      >
        <span :style="styleButtonColor(floor)"></span>
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: "FloorItem",
  data() {
    return {
      floorHeight: 0,
      halls: [1, 2, 3],
    };
  },
  props: {
    floor: {
      type: Number,
      required: true,
    },
    callsArr: {
      type: Array,
      required: true,
    },
    heightFloor: {
      type: Number,
    },
    items: {
      type: Array,
      required: true,
    },
  },
  mounted() {
    this.floorHeight = this.$refs.height.offsetHeight;
    // console.log(this.floorHeight);
  },
  methods: {
    callfunc(floor) {
      this.$emit("select-floor", floor, this.floorHeight);
    },
    styleButtonBorder(floor) {
      return {
        border: this.callsArr.find((i) => i.includes(floor))
          ? "2px solid red"
          : "1px solid #000",
      };
    },
    styleButtonColor(floor) {
      return {
        backgroundColor: this.callsArr.find((i) => i.includes(floor))
          ? "red"
          : "green",
      };
    },
  },
};
</script>

<style scoped>
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
