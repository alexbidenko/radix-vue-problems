<script lang="ts" setup generic="T extends object">
import type { VNode } from 'vue';

import { ComboboxInput } from '#components';

const props = defineProps<{
  placeholder: string;
  loading?: boolean;
  options: T[];
  disabled?: boolean;
  getOptionKey: (value: T) => string;
  getOptionLabel: (value: T) => string;
  preventClosing?: (value: T) => boolean;
}>();
const emit = defineEmits<{
  (event: 'select', value: T): void;
}>();
const query = defineModel<string>('query', {
  required: true,
});
defineSlots<{
  label: (props: { option: T }) => VNode[];
  hint: (props: { option: T }) => VNode[];
}>();

const isOptionsShown = ref(false);

const onSelect = (event: Event, value: T) => {
  if (props.preventClosing?.(value)) event.preventDefault();

  emit('select', value);
};
</script>

<template>
  <ComboboxRoot
    v-model:search-term="query"
    v-model:open="isOptionsShown"
    :disabled="props.disabled"
    :filter-function="(val) => val"
    class="baseSearchSelect"
    :class="{ baseSearchSelect_disabled: props.disabled }"
  >
    <ComboboxAnchor class="baseSearchSelect__label">
      <TextField
        @keydown.enter.prevent
        :input-component="ComboboxInput"
        container-tag="span"
        :placeholder="props.placeholder"
        bordered
        autocomplete="none"
        class="baseSearchSelect__input"
        :class="{ baseSearchSelect__input_expanded: isOptionsShown }"
      />
      <Loader v-if="props.loading" dark class="baseSearchSelect__loader" />
    </ComboboxAnchor>

    <ComboboxPortal to="#modals">
      <Transition name="fade">
        <ComboboxContent
          v-if="options.length"
          position="popper"
          :side-offset="-1"
          class="baseSearchSelect__content"
        >
          <ComboboxViewport class="baseSearchSelect__options">
            <ComboboxGroup>
              <ComboboxItem
                v-for="option in props.options"
                @select="onSelect($event, option)"
                :key="props.getOptionKey(option)"
                :value="props.getOptionLabel(option)"
                class="baseSearchSelect__option"
              >
                <slot name="label" :option="option" />
                <span class="baseSearchSelect__hint">
                  <slot name="hint" :option="option" />
                </span>
              </ComboboxItem>
            </ComboboxGroup>
          </ComboboxViewport>
        </ComboboxContent>
      </Transition>
    </ComboboxPortal>
  </ComboboxRoot>
</template>

<style lang="scss">
.baseSearchSelect {
  position: relative;

  &_disabled {
    pointer-events: none;
  }

  &__label {
    position: relative;
    display: block;
  }

  & &__input {
    & input {
      padding-right: 39px;
      width: 100%;
      transition: border-radius 0.3s ease;
    }

    &_expanded {
      border-bottom-left-radius: 0;
      border-bottom-right-radius: 0;
    }
  }

  &__loader {
    top: 0;
    right: 0;
    transform-origin: 50% 50%;
    transform: scale(0.7);
    position: absolute;
  }

  &__content {
    width: var(--radix-combobox-trigger-width);
    z-index: 10;
  }

  &__options {
    background-color: #fff;
    border: 1px solid #121212;
    box-shadow: 0 5px 20px rgba(0, 0, 0, 0.1);
    border-radius: 0 0 4px 4px;
    z-index: 1;
    padding: 7px 0;
    display: flex;
    flex-direction: column;
    max-height: 275px;
    box-sizing: border-box;
  }

  &__option {
    font-size: 14px;
    line-height: 120%;
    color: #121212;
    padding: 8px 15px;
    background-color: transparent;
    border: none;
    cursor: pointer;
    transition: background-color 0.15s ease;
    text-align: left;
    white-space: pre-wrap;

    &:hover,
    &[data-highlighted] {
      background-color: #f2f2f2;
    }
  }

  &__hint {
    margin-top: 2px;
    font-size: 12px;
    line-height: 120%;
    color: #c0c0c0;
    display: block;

    &:empty {
      display: none;
    }
  }
}
</style>
