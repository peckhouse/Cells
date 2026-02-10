<template>
  <Primitive
    class="cell-dropdown"
    :as
  >
    <SelectRoot v-bind="forwarded">
      <SelectTrigger v-bind="forwarded" class="cell-dropdown__trigger">
        <SelectValue v-bind="forwarded" class="cell-dropdown__trigger-value">
          <template v-if="triggerType === 'badge' && (modelValue as AcceptableValue[])?.length > 0">
            <div class="cell-dropdown__badges">
              <span
                v-for="value in selectedValues"
                :key="value"
                class="cell-dropdown__badge"
              >
                {{ getOptionText(value) }}
              </span>
            </div>
          </template>

          <template v-if="triggerType === 'hidden'">
            <span>
              {{ forwarded.placeholder }}
            </span>
          </template>
        </SelectValue>
        <ChevronDownIcon class="cell-dropdown__trigger-icon" />
      </SelectTrigger>

      <SelectPortal>
        <SelectContent
          class="cell-dropdown__content"
          position="popper"
          data-side="bottom"
          :side-offset="4"
        >
          <SelectViewport class="cell-dropdown__viewport">
            <SelectGroup>
              <SelectItem
                v-for="(option, index) in options"
                :key="index"
                class="cell-dropdown__item"
                :value="option?.value"
                :text-value="option?.text"
              >
                <SelectItemIndicator v-if="props.indicator && props.indicator === 'left'">
                  <slot name="item-selected-indicator">
                    <CellCheckbox :model-value="true" class="cell-dropdown__item-selector" />
                  </slot>
                </SelectItemIndicator>

                <SelectItemText class="cell-dropdown__item-text" :value="option?.value">
                  {{ option?.text }}
                </SelectItemText>

                <SelectItemIndicator v-if="props.indicator && props.indicator === 'right'">
                  <slot name="item-selected-indicator">
                    <CellCheckbox :model-value="true" class="cell-dropdown__item-selector" />
                  </slot>
                </SelectItemIndicator>
              </SelectItem>
            </SelectGroup>
          </SelectViewport>
        </SelectContent>
      </SelectPortal>
    </SelectRoot>
  </Primitive>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import {
  Primitive,
  SelectContent,
  SelectItemIndicator,
  SelectItem,
  SelectItemText,
  SelectGroup,
  SelectPortal,
  SelectRoot,
  SelectTrigger,
  SelectValue,
  SelectViewport,
  type SelectRootEmits,
  type SelectRootProps,
  type SelectValueProps,
  useForwardPropsEmits,
  type AcceptableValue
} from 'reka-ui'

import CellCheckbox from '../checkbox/CellCheckbox.vue'
import ChevronDownIcon from '@/assets/icons/chevron-down.svg'

defineOptions({ inheritAttrs: false })

type Option = {
  value: string
  text?: string
}

type TriggerType = 'default' | 'badge' | 'hidden'

const props= withDefaults(defineProps<SelectRootProps & SelectValueProps & {
  options: Option[],
  as?: string,
  triggerType?: TriggerType
  indicator?: 'left' | 'right' | null
}>(), {
  as: 'div',
  triggerType: 'badge',
  indicator: null
})

const { options, as, triggerType, ...forwardedProps } = props
const emits = defineEmits<SelectRootEmits>()

const forwarded = useForwardPropsEmits(forwardedProps, emits)

// Compute selected values for badge display
const selectedValues = computed(() => {
  if (!props.modelValue) return []
  return Array.isArray(props.modelValue) ? props.modelValue : [props.modelValue]
})

// Get option text by value
const getOptionText = (value: string) => {
  const option = options.find(opt => opt.value === value)
  return option?.text || value
}
</script>

<style scoped lang="scss">
.cell-dropdown {

  &__trigger {
    @include button-reset();

    border-radius: var(--global-border-radius-medium);
    background-color: var(--c-white-100);
    border: 1px solid var(--c-gray-80);
    justify-content: space-between;
    align-items: center;
    min-width: 200px;
    padding: 0 12px;
    display: flex;
    height: 32px;
    gap: 8px;
  }

  &__trigger-value {
    color: var(--c-gray-120);
    font-size: 14px;
  }

  &__trigger-icon {
    fill: var(--c-gray-120);
    height: 16px;
    width: 16px;
  }

  &__badges {
    align-items: center;
    flex-wrap: wrap;
    display: flex;
    gap: 4px;
  }

  &__badge {
    border-radius: var(--global-border-radius-small, 4px);
    background-color: var(--c-gray-70);
    color: var(--c-gray-120);
    white-space: nowrap;
    padding: 2px 8px;
    font-weight: 500;
    font-size: 12px;
  }
}

:deep(.cell-dropdown__content) {
  border-radius: var(--global-border-radius-medium);
  background-color: var(--c-white-100);
  border: 1px solid var(--c-gray-80);
  box-shadow: var(--shadow-medium);
  min-width: 200px;
  outline: none;
  padding: 8px;
  width: 100%;
  z-index: 1;

  &[data-state="open"] {
    animation: selectFadeIn var(--transition-global-duration) var(--transition-global-timing-function);
  }

  &[data-state="closed"] {
    animation: selectFadeOut var(--transition-global-duration) var(--transition-global-timing-function);
  }
}

:deep(.cell-dropdown__viewport) {
  outline: none;

  &[data-state="open"] {
    animation: selectFadeIn var(--transition-global-duration) var(--transition-global-timing-function);
  }

  &[data-state="closed"] {
    animation: selectFadeOut var(--transition-global-duration) var(--transition-global-timing-function);
  }
}

:deep(.cell-dropdown__item) {
  border-radius: var(--global-border-radius-medium);
  justify-content: space-between;
  align-items: center;
  padding: 0px 8px;
  cursor: pointer;
  display: flex;
  outline: none;
  height: 32px;


  &[aria-selected="true"] {
    background-color: var(--c-gray-70);
  }

  &:hover {
    background-color: var(--c-gray-60);
    outline: none;
    border: none;
  }
}

:deep(.cell-dropdown__item-text) {
  color: var(--c-gray-120);
  font-size: 14px;
}

:deep(.cell-dropdown__item-selector) {
  pointer-events: none;
  // position: absolute;
  // right: 8px;
}

@keyframes selectFadeIn {
  from {
    transform: translate3d(0, -8px, 0);
    opacity: 0;
  }
  to {
    transform: translate3d(0, 0, 0);
    opacity: 1;
  }
}

@keyframes selectFadeOut {
  from {
    transform: translate3d(0, 0, 0);
    opacity: 1;
  }
  to {
    transform: translate3d(0, -8px, 0);
    opacity: 0;
  }
}
</style>
