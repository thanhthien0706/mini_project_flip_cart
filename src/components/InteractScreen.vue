<template>
  <div class="screenInteract">
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
        :imagBackFaceUrl="`images/${card}.png`"
        :card="{ index: index, value: card }"
        @onFlip="checkRule($event)"
        :cardsContext="cardsContext"
      />
    </div>
  </div>
</template>

<script>
import CardFlip from "./Card.vue";
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

  methods: {
    checkRule(card) {
      if (this.rules.length === 2) return false;

      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        console.log("right...");
        // add class "disabled" to components card
        this.$refs[`card-${this.rules[0].index}`].onEnabledDisabledModels();
        this.$refs[`card-${this.rules[1].index}`].onEnabledDisabledModels();
        // reset rules to []
        this.rules = [];

        const disabledElements = document.querySelectorAll(
          ".screenInteract .card.disabled"
        );

        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        setTimeout(() => {
          // close two card
          this.$refs[`card-${this.rules[0].index}`].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`].onFlipBackCard();
          // reset rules to []
          this.rules = [];
        }, 700);
      } else return false;
    },
  },

  components: {
    CardFlip,
  },
};
</script>

<style scoped lang="css">
.screenInteract {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background-color: var(--dark);
  color: var(--light);
}

.screen__inner {
  /* width: 424px; */
  display: flex;
  flex-wrap: wrap;
  margin: 2rem auto;
}
</style>
