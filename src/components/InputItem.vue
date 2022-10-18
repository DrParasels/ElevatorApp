<template>
  <div class="input-section">
    <input
      class="input"
      v-bind:value="elevatorsNumbers"
      @input="elevatorsNumbers = $event.target.value"
      type="number"
      placeholder="Количество лифтов"
    />
    <input
      class="input"
      :value="floorsNumbers"
      @input="floorsNumbers = $event.target.value"
      type="number"
      placeholder="Количество этажей"
    />
    <button @click="confirm" class="input-btn">Применить</button>
  </div>
</template>

<script>
export default {
  name: "InputItem",
  data() {
    return {
      elevatorsNumbers: null,
      floorsNumbers: null,
    };
  },
  methods: {
    confirm() {
      this.$emit(
        "makefloors-elevators",
        this.elevatorsNumbers,
        this.floorsNumbers
      );
      this.elevatorsNumbers = null;
      this.floorsNumbers = null;
    },
  },
};
</script>

<style scoped>
.input-section {
  display: flex;
  justify-content: start;
  margin-bottom: 30px;
}

.input {
  -moz-appearance: textfield;
  -webkit-appearance: textfield;
  appearance: textfield;
  padding: 10px;
  margin-right: 15px;
  border: 1px solid teal;
  border-radius: 5px;
}

input[type="number"]::-webkit-outer-spin-button,
input[type="number"]::-webkit-inner-spin-button {
  display: none;
}

.input-btn {
  background: teal;
  color: #fff;
  font-size: 16px;
  border: 1px solid teal;
  border-radius: 5px;
  cursor: pointer;
  padding: 0 15px;
}

.input-btn:hover {
  background: rgb(26, 99, 99);
}

.input-btn:active {
  background: teal;
}
</style>
