<template>
  <Primitive
    class="cell-checkbox"
    :as="props.as"
  >
    <span class="cell-checkbox__content" :class="[`cell-checkbox__content--${props.direction}`]">
      <CheckboxRoot
        v-model="checked"
        v-bind="$attrs"
        :id
        class="cell-checkbox__root"
      >
        <CheckboxIndicator class="cell-checkbox__indicator">
          <IndeterminatedIcon v-if="checked === 'indeterminate'" />
          <CheckedIcon v-else />
        </CheckboxIndicator>
      </CheckboxRoot>

      <label v-if="$slots.default" :for="id" class="cell-checkbox__label"><slot /></label>
    </span>
  </Primitive>
</template>

<script setup lang="ts">
import { CheckboxIndicator, CheckboxRoot, Primitive, type CheckboxRootProps } from 'reka-ui'
import { useId } from 'vue'

import CheckedIcon from '@/assets/icons/checked.svg'
import IndeterminatedIcon from '@/assets/icons/indeterminated.svg'

export type CellCheckboxValue = CheckboxRootProps['modelValue']

defineOptions({
  inheritAttrs: false
})

const checked = defineModel<CellCheckboxValue>()
const id = useId()

type Props = {
  direction?: 'ltr' | 'rtl'
  as?: string
}
const props = withDefaults(defineProps<Props>(), {
  as: 'div',
  direction: 'ltr'
})
</script>

<style scoped lang="scss">
.cell-checkbox {
  $cellCheckbox: &;

  :deep(.cell-checkbox__root) {
    @include button-reset();

    transition: all var(--transition-global-duration) var(--transition-global-timing-function);

    background-color: var(--cell-checkbox-background-color);
    border-radius: var(--cell-checkbox-border-radius);
    box-shadow: var(--cell-checkbox-shadow);
    border: var(--cell-checkbox-border);
    height: var(--cell-checkbox-size);
    width: var(--cell-checkbox-size);
    font-size: 0;

    &:hover {
      background-color: var(--cell-checkbox-background-color-hover);
      border: var(--cell-checkbox-border-hover);
    }

    &[aria-checked='true'],
    &[aria-checked='mixed'] {
      background-color: var(--cell-checkbox-background-color-checked);
      border: var(--cell-checkbox-border-checked);
      box-shadow: none;

      &:hover {
        background-color: var(--cell-checkbox-background-color-checked-hover);
        border: var(--cell-checkbox-border-checked-hover);
      }
    }

    &:disabled {
      background-color: var(--cell-checkbox-background-color-disabled);
      border: var(--cell-checkbox-border-disabled);
      pointer-events: none;
      user-select: none;
      box-shadow: none;
    }
  }

  :deep(.cell-checkbox__indicator) {
    height: calc(var(--cell-checkbox-size) - var(--cell-checkbox-border-width) * 2);
    width: calc(var(--cell-checkbox-size) - var(--cell-checkbox-border-width) * 2);
    display: block;

    svg{
      height: calc(var(--cell-checkbox-size) - var(--cell-checkbox-border-width) * 2);
      width: calc(var(--cell-checkbox-size) - var(--cell-checkbox-border-width) * 2);
      display: block;

      path {
        stroke: var(--cell-checkbox-icon-color);
      }
    }
  }

  &__label {
    font-size: var(--cell-checkbox-label-font-size);
    font-weight: var(--cell-checkbox-label-font-weight);
    color: var(--cell-checkbox-label-color);
  }

  &__content {
    align-items: center;
    display: flex;
    
    &--ltr {
      justify-content: flex-start;
      
      #{$cellCheckbox}__label {
        margin-left: 8px;
      }
    }

    &--rtl {
      justify-content: flex-end;
      
      #{$cellCheckbox}__label {
        margin-right: 8px;
      }
    }
  }
}
</style>
