<template>
  <div class="range">
    <input
      v-model="progress"
      ref="range"
      type="range"
      class="range__input"
      :step="step"
      :max="max"
    />

    <div class="range__bar">
      <div class="range__elapsed" :style="`width: ${elapsed}%`"></div>
    </div>
  </div>
</template>

<script lang="ts">
import Vue from 'vue'

const name = 'CustomRange'

export default Vue.extend({
  name,

  props: {
    value: {
      type: [Number, String],
      default: 0,
    },
    step: {
      type: [Number, String],
      default: 0.001,
    },
    max: {
      type: [Number, String],
      default: 100,
    },
  },

  computed: {
    range(): HTMLInputElement {
      return this.$refs.range as HTMLInputElement
    },
    progress: {
      get(): number | string {
        return this.value
      },
      set(value: number): void {
        this.$emit('input', value)
      }
    },
    elapsed(): number {
      const percentage = +this.progress * 100 / +this.max
      /** Some magic to compensate native slider-thumb width */
      const edge = percentage < 0.1 || percentage > 99.9
      const offset = edge ? 0 : (percentage - 50) / 25

      return percentage - offset
    }
  },
});
</script>

<style scoped lang="scss">
$height: 8px;
$bar-color: #B2B0B1;
$elapsed-color: #FF031C;

.range {
  position: relative;

  &__input,
  &__bar {
    position: absolute;
    top: 0;
    bottom: 0;
    right: 0;
    left: 0;
    margin: 0;
    padding: 0;
    height: $height;
    width: 100%;
  }

  &__input {
    cursor: pointer;
    opacity: 0;
  }

  &__bar {
    pointer-events: none;
    background-color: $bar-color;
    border-radius: 4px;
    overflow: hidden;
  }

  &__elapsed {
    height: 100%;
    background-color: $elapsed-color;
    border-radius: 4px 0 0 4px;
  }
}
</style>
