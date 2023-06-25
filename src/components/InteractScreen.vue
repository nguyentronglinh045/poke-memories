<template>
  <div class="screen">
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <card-flip
        v-for="(card, index) in cardsContext"
        :key="index"
        :ref="`card-${index}`"
        :imgBackFaceUrl="`images/${card}.png`"
        :card="{ index: index, value: card }"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>
<script>
import CardFlip from "./CardFlip.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  data() {
    return {
      rules: [],
    };
  },
  components: {
    CardFlip,
  },
  methods: {
    checkRule(card) {
      if (this.rules.length === 2) {
        return false;
      }
      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("Right");
        // add class disabled to componet card
        this.$refs[`card-${this.rules[0].index}`][0].onEnableDisableMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnableDisableMode();
        // reset rules to []
        this.rules = [];
        const disabledElement = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElement &&
          disabledElement.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 1000);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        console.log("wrong");
        // close two cards
        setTimeout(() => {
          // this.$refs[`card-${this.rules[1].index}`].onFlipBackCard();
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          // reset rule to []
          this.rules = [];
        }, 800);
      } else return false;
    },
  },
};
</script>

<style lang="css" scoped>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  inset: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}
.screen__inner {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
