<template>
  <div ref="height" class="floor">
    <div class="lift-shaft"></div>
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
    };
  },
  props: {
    floor: {
      type: Number,
      required: true,
    },
    callsFloors: {
      type: Array,
      required: true,
    },
    heightFloor: {
      type: Number,
    },
  },
  mounted() {
    this.floorHeight = this.$refs.height.offsetHeight;
  },
  methods: {
    callfunc(floor) {
      this.$emit("select-floor", floor);
    },
    styleButtonBorder(floor) {
      return {
        border: this.callsFloors.includes(floor)
          ? "2px solid red"
          : "1px solid #000",
      };
    },
    styleButtonColor(floor) {
      return {
        backgroundColor: this.callsFloors.includes(floor) ? "red" : "green",
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
