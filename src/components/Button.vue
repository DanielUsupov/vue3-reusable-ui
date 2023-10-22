

<script lang="ts">
export const ButtonVariant = {
  primary: 'primary',
  secondary: 'secondary',
  tertiary: 'tertiary',
} as const

export const ButtonSize = {
  sm: 'sm',
  base: 'base',
  lg: 'lg',
} as const

export const variantClasses = {
  [ButtonVariant.primary]:
    'text-white shadow hover:shadow-md active:shadow bg-primary-700 hover:bg-primary-800 active:bg-primary-900 disabled:bg-disabled-300',
  [ButtonVariant.secondary]:
    'text-primary-700 hover:bg-primary-100 hover:text-primary-800 active:bg-primary-200 active:text-primary-900 ring-1 ring-inset ring-primary-700 shadow hover:shadow-md active:shadow hover:ring-primary-800 active:ring-primary-900 disabled:ring-1 disabled:ring-disabled-300 disabled:bg-white/50',
  [ButtonVariant.tertiary]:
    'text-primary-700 hover:bg-primary-100 hover:text-primary-800 active:bg-primary-200 active:text-primary-900 disabled:bg-transparent',
};

</script>

<script lang="ts" setup>
import { useAttrsRef } from '@/composables/reactiveContext';
import { type ConcreteComponent, toRefs, computed } from 'vue'

type Props = {
  size?: keyof typeof ButtonSize,
  variant?: keyof typeof ButtonVariant,
  disabled?: boolean,
  square?: boolean,
  tag: string | ConcreteComponent,
}

const props = withDefaults(defineProps<Props>(), {
  size: ButtonSize.base,
  variant: ButtonVariant.primary,
  disabled: false,
  square: false,
  tag: 'button',
})

const { size, tag, square } = toRefs(props);

const sizeClasses = computed(() => {
  switch (size.value) {
    case ButtonSize.sm:
      return [square.value ? 'p-1.5' : 'leading-5 text-sm py-1.5 px-3', 'gap-1.5'];
    case ButtonSize.lg:
      return [square.value ? 'p-4' : 'py-3 leading-6 px-6', 'gap-3'];
    default:
      return [square.value ? 'p-2' : 'py-2 leading-6 px-4', 'gap-2'];
  }
})

const tagWithDefaults = computed(() => tag?.value || 'button');
const attrs = useAttrsRef();
const type = computed(
  () =>
    attrs.value.type ??
    (typeof tagWithDefaults.value === 'string' && tagWithDefaults.value.toLowerCase() === 'button'
      ? 'button'
      : undefined),
);
</script>

<template>
  <component
    :is="tagWithDefaults"
    :type="type"
    :disabled="disabled"
    :class="[
      'inline-flex items-center justify-center font-medium text-base focus-visible:outline focus-visible:outline-offset rounded-md disabled:text-disabled-500 disabled:bg-disabled-300 disabled:shadow-none disabled:ring-0 disabled:cursor-not-allowed',
      sizeClasses,
      variantClasses[variant],
    ]"
    data-testid="button"
  >
    <slot v-if="$slots.prefix" name="prefix" />
    <slot />
    <slot v-if="$slots.suffix" name="suffix" />
  </component>
</template>
