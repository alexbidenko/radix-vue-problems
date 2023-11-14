<script lang="ts" setup>
import type {
  ComponentPublicInstance,
  InputHTMLAttributes,
} from 'vue';

type ReassignAttributes<T> = Omit<T, 'value' | 'readonly'> & {
  readonly?: boolean;
};

interface Props
  extends /* @vue-ignore */ ReassignAttributes<InputHTMLAttributes> {
  bordered?: boolean;
  failed?: boolean;
  containerTag?: string;
  inputComponent?: ComponentPublicInstance;
}

defineOptions({ inheritAttrs: false });
const props = withDefaults(defineProps<Props>(), { containerTag: 'label' });
const attrs = useAttrs();

const fieldRef = ref<HTMLInputElement | HTMLTextAreaElement>();

const value = defineModel<string>('value', {
  default: '',
});

const inputAttrs = computed(() => {
  const { class: _, ...rest } = attrs;
  return rest;
});

defineExpose({
  $field: fieldRef,
});
</script>

<template>
  <Component
    :is="props.containerTag"
    class="baseInput"
    :class="[
      { baseInput_bordered: props.bordered, baseInput_failed: props.failed },
      attrs.class,
    ]"
  >
    <Component
      v-if="props.inputComponent"
      v-model="value"
      :is="props.inputComponent"
      :ref="fieldRef"
      class="baseInput__field"
      v-bind="inputAttrs"
    />
    <input
      v-else
      v-model="value"
      :value="value"
      ref="fieldRef"
      class="baseInput__field"
      v-bind="inputAttrs"
    />
  </Component>
</template>

<style lang="scss">
.baseInput {
  --field-height: 40px;

  position: relative;
  display: block;
  color: #121212;
  border-radius: 4px;

  &__field {
    display: block;
    width: 100%;
    height: var(--field-height);
    padding: 11px 16px;
    background-color: #ffffff;
    border: 1px solid #ffffff;
    font-size: 14px;
    outline: none;
    transition:
      color 0.3s ease,
      border-color 0.3s ease;
    resize: none;
    color: inherit;
    border-radius: inherit;

    &:disabled {
      color: #c0c0c0;
    }

    &::placeholder {
      color: #c0c0c0;
    }
  }

  &_bordered &__field {
    border-color: #121212;

    &:disabled {
      border-color: #c0c0c0;
    }
  }

  &_failed &__field {
    border-color: #eb5757;
    color: #eb5757;
  }

  &__clear {
    position: absolute;
    right: 0;
    top: 0;
    height: var(--field-height);
    aspect-ratio: 1 / 1;
    cursor: pointer;
    padding: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background-color: transparent;
    transition: background-color 0.3s ease;
    border: none;
    border-radius: inherit;

    &:hover {
      background-color: #121212;
      color: #fff;
    }
  }

  &__icon {
    width: 24px;
    height: 24px;
  }
}
</style>
