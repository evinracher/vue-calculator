<template>
  <div class="container mt-5 p-0">
    <div class="displayer mb-4 output" :class="{ 'text-success': processed }">
      {{ display }}
    </div>
    <div class="row text-center">
      <div class="col-4 p-1" v-for="number in numbers" :key="number">
        <button
          :class="`digit-${number}`"
          class="btn btn-secondary w-100"
          @click="inputADigit(number)"
        >
          {{ number }}
        </button>
      </div>
      <div
        class="col-4 p-1"
        v-for="(operator, index) in operators"
        :key="index + operator"
      >
        <button
          :class="`${operatorsNames[index]}`"
          class="btn btn-primary w-100"
          :disabled="(emptyDisplay && index !== 1) || needDigit"
          @click="inputAnOperator(operator)"
        >
          {{ operator }}
        </button>
      </div>
      <div class="col-4 p-1">
        <button
          class="btn btn-danger w-100 eq"
          :disabled="wasAnOperator || !display"
          @click="process"
        >
          =
        </button>
      </div>
      <div class="p-1">
        <button class="btn btn-dark w-100 clear" @click="clear">Clear</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      numbers: ["1", "2", "3", "4", "5", "6", "7", "8", "9", "0"],
      operators: ["+", "-", "*", "/"],
      operatorsNames: ["op-add", "op-sub", "op-mul", "op-div"],
      display: "",
      result: "",
      needDigit: false,
      leadingZero: true,
      processed: false,
    };
  },
  methods: {
    clear() {
      this.display = "";
      this.needDigit = false;
      this.leadingZero = true;
      this.processed = false;
    },
    inputADigit(digit) {
      if (this.processed) {
        this.display = "";
        this.processed = false;
      }
      if (this.last === "0") {
        if (this.leadingZero) {
          this.replaceLastOne(digit);
          if (digit !== "0") this.leadingZero = false;
        } else {
          this.display += digit;
        }
      } else {
        if (this.wasAnOperator && digit !== "0") this.leadingZero = false;
        this.display += digit;
      }

      if (this.needDigit) {
        this.needDigit = false;
      }
    },
    inputAnOperator(operator) {
      if (this.processed) {
        this.processed = false;
      }
      if (this.wasAnOperator) {
        if (this.wasSubOrAdd) {
          this.replaceLastOne(operator);
        } else {
          if (operator === "-") {
            this.display += operator;
            this.needDigit = true;
          } else {
            this.replaceLastOne(operator);
          }
        }
      } else {
        this.display += operator;
      }
      this.leadingZero = true;
    },
    replaceLastOne(char) {
      this.display = this.display.substring(0, this.lastIndex) + char;
    },
    process() {
      const res = Math.floor(eval(this.display));
      if (res === Infinity || res === -Infinity) {
        this.clear();
      } else {
        this.display = res;
      }
      this.processed = true;
    },
  },
  computed: {
    lastIndex() {
      return this.display.length - 1;
    },
    last() {
      return this.display[this.lastIndex];
    },
    emptyDisplay() {
      return this.display === "";
    },
    wasADigit() {
      return this.numbers.includes(this.last);
    },
    wasAnOperator() {
      return this.operators.includes(this.last);
    },
    wasSubOrAdd() {
      return this.last === "-" || this.last === "+";
    },
  },
};
</script>

<style scoped>
.displayer {
  border: 0.5rem solid darkgray;
  border-radius: 0.5rem;
  padding: 1rem;
  text-align: right;
  min-height: 6rem;
  font-weight: bold;
  font-size: 2rem;
}
</style>