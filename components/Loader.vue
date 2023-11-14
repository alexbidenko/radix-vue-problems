<script lang="ts" setup>
const props = defineProps<{
  dark?: boolean;
  area?: boolean;
}>();
</script>

<template>
  <div
    class="baseLoader"
    :class="{ baseLoader_dark: props.dark, baseLoader_area: props.area }"
  >
    <div class="baseLoader__content">
      <span class="baseLoader__point" />
      <span class="baseLoader__point" />
      <span class="baseLoader__point" />
    </div>
  </div>
</template>

<style lang="scss">
.baseLoader {
  width: fit-content;
  height: fit-content;
  display: flex;
  justify-content: center;

  &_dark &__point {
    background-color: #121212;
  }

  &_area {
    width: 100%;
    padding: 24px 12px;
  }

  &__content {
    display: flex;
    align-items: center;
    justify-content: center;
    width: fit-content;
    height: 40px;
  }

  &__point {
    width: 8px;
    height: 8px;
    background-color: #ffffff;
    border-radius: 50%;
    margin: 3px;

    &:nth-child(1) {
      animation: points 0.75s ease infinite;
    }

    &:nth-child(2) {
      animation: points-inverted 0.75s ease infinite;
    }

    &:nth-child(3) {
      animation: points 0.75s ease infinite;
    }
  }

  @keyframes points {
    0% {
      transform: translateY(8px);
    }

    50% {
      transform: translateY(-8px);
    }

    100% {
      transform: translateY(8px);
    }
  }

  @keyframes points-inverted {
    0% {
      transform: translateY(-8px);
    }

    50% {
      transform: translateY(8px);
    }

    100% {
      transform: translateY(-8px);
    }
  }
}
</style>
