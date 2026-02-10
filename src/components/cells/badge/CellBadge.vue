<template>
  <Primitive
    v-bind="$attrs"
    class="cell-badge"
    :as
    :class="[
      `cell-badge--${props.color}`,
      `cell-badge--${props.size}`,
      {'cell-badge--as-button': props.asButton}
    ]"
    @click="emit('click')"
  >
    <span class="cell-badge__content">
      <slot />

      <button
        v-if="props.closable"
        type="button"
        class="cell-badge__close"
        :aria-label="props.closeAriaLabel"
        @click.stop="handleClose"
      >
        <slot name="close-icon">
          <CrossIcon />
        </slot>
      </button>
    </span>
  </Primitive>
</template>

<script setup lang="ts">
import { Primitive } from 'reka-ui'
import { computed } from 'vue'

import CrossIcon from '@/assets/icons/cross.svg'

type ColorSchemeName = 'gray' | 'green' | 'red' | 'blue' | 'orange' | 'yellow'

type Emit = {
  (e: 'close'): void
  (e: 'click'): void
}
const emit = defineEmits<Emit>()

type Props = {
  color?: ColorSchemeName
  size?: 'small' | 'medium' | 'large'
  closable?: boolean
  closeAriaLabel?: string
  href?: string
  to?: string
  asButton?: boolean
}
const props = withDefaults(defineProps<Props>(), {
  color: 'gray',
  size: 'medium',
  inline: false,
  closable: false,
  closeAriaLabel: 'Close badge',
  asButton: false
})

const as = computed(() => {
  // Note: to prop is available for future router integration
  // Currently renders as 'a' tag when href or to is provided
  if (props.href || props.to) return 'a'
  // Render as button if asButton prop is true
  if (props.asButton) return 'button'
  return 'div'
})

const handleClose = () => {
  emit('close')
}
</script>

<style scoped lang="scss">
.cell-badge {
  $baseBadgeClass: &;
  $baseBadgeSizesList: ('small', 'medium', 'large');
  $baseBadgeColorsList: ('gray', 'green', 'red', 'blue', 'orange', 'yellow');

  @include button-reset();

  transition: all var(--transition-global-duration) var(--transition-global-timing-function);

  border-radius: var(--global-border-radius-medium);
  font-weight: var(--cell-badge-font-weight);
  box-sizing: border-box;
  vertical-align: middle;
  text-decoration: none;
  white-space: nowrap;
  font-size: 0;
  cursor: text;

  &--as-button {
    cursor: pointer;
  }

  @each $size in $baseBadgeSizesList {
    &--#{$size} {
      font-size: var(--cell-badge-font-size-#{$size});
      padding: var(--cell-badge-padding-#{$size});
      height: var(--cell-badge-height-#{$size});
      
      #{$baseBadgeClass}__content {        
        height: calc(var(--cell-badge-height-#{$size}) - (var(--cell-badge-border-size) * 2));
      }

      .cell-badge__close {
        :deep(svg) {
          height: var(--cell-badge-close-icon-size-#{$size});
          width: var(--cell-badge-close-icon-size-#{$size});
        }
      }
    }
  }

  @each $color in $baseBadgeColorsList {
    &--#{$color} {
      @include badge-color-scheme($color);
    }
  }

  &__content {
    text-overflow: ellipsis;
    align-items: center;
    gap: spacing(.5);
    overflow: hidden;
    line-height: 1;
    display: flex;
    min-width: 0;
  }

  &__close {
    @include button-reset();

    transition: all var(--transition-global-duration) var(--transition-global-timing-function);

    border-radius: var(--global-border-radius-small);
    cursor: pointer;
    padding: 0;
    margin: 0;

    &:hover {
      opacity: 0.7;
    }

    &:active {
      opacity: 0.5;
    }

    :deep(svg) {
      display: block;
    }
  }

  // Clickable badge styles
  &[href],
  &[to] {
    cursor: pointer;

    &:hover {
      opacity: 0.85;
    }

    &:active {
      opacity: 0.7;
    }
  }
}
</style>
