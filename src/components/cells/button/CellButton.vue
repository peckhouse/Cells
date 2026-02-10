<template>
  <Primitive
    v-bind="$attrs"

    class="cell-button"
    :as
    :class="[
      `cell-button--${props.color}`,
      `cell-button--${props.size}`,
      { 'cell-button--is-icon': props.icon }
    ]"
  >
    <span class="cell-button__content">
      <slot name="left-icon" />
      <slot />
      <slot name="right-icon" />
    </span>
  </Primitive>
</template>

<script setup lang="ts">
import { Primitive } from 'reka-ui'
import { computed } from 'vue'
import { RouterLink, type RouteLocationRaw } from 'vue-router'

// import { ColorSchemeName } from '@/types/colors'

type ColorSchemeName = 'white' | 'green' | 'red' | 'ghost-white' | 'ghost-red'

type Props = {
  color?: ColorSchemeName
  size?: 'small' | 'medium' | 'large'
  icon?: boolean
  href?: string
  to?: RouteLocationRaw
}
const props = withDefaults(defineProps<Props>(), {
  color: 'white',
  size: 'medium',
  icon: false,
  as: 'button'
})

const as = computed(() => {
  if (props.to) return RouterLink
  if (props.href) return 'a'
  return 'button'
})
</script>

<style scoped lang="scss">
.cell-button {
  $baseButtonClass: &;
  $baseButtonSizesList: ('small', 'medium', 'large');
  $baseButtonColorsList: ('white', 'green', 'red', 'ghost-white', 'ghost-red');

  @include button-reset();

  transition: all var(--transition-global-duration) var(--transition-global-timing-function);
  border-radius: var(--global-border-radius-medium);
  font-weight: var(--cell-button-font-weight);

  &:disabled {
    cursor: default;
  }

  @each $size in $baseButtonSizesList {
    &--#{$size} {
      font-size: var(--cell-button-font-size-#{$size});
      padding: var(--cell-button-padding-#{$size});
      height: var(--cell-button-height-#{$size});

      &#{$baseButtonClass}--is-icon {
        padding: var(--cell-button-icon-padding-#{$size});
      }

      :deep(svg) {
        height: var(--cell-button-icon-size-#{$size});
        width: var(--cell-button-icon-size-#{$size});
      }
    }
  }

  @each $color in $baseButtonColorsList {
    &--#{$color} {
      @include button-color-scheme($color);
    }
  }

  &__content {
    justify-content: center;
    align-items: center;
    display: flex;
    gap: 8px;
  }
}
</style>
