<template>
  <div
    ref="flyoutRef"
    :class="{
      relative: !detachFlyoutBody,
      static: detachFlyoutBody,
    }"
  >
    <slot
      name="trigger"
      :is-open="isOpen"
      :toggle="toggle"
      :open="open"
      :close="close"
    />
    <div
      v-if="isOpen"
      ref="flyoutBody"
      class="absolute z-[999] p-5 bg-neutral-100 text-neutral-900 font-5 shadow-large rounded-[10px] min-w-[182px]"
      :class="{
        'w-full': fullWidth,

        'left-1/2 -translate-x-1/2':
          align === FLYOUT_ALIGN.BOTTOM_CENTER || align === FLYOUT_ALIGN.TOP_CENTER,
        'left-0': align === FLYOUT_ALIGN.BOTTOM_LEFT || align === FLYOUT_ALIGN.TOP_LEFT,
        'right-0': align === FLYOUT_ALIGN.BOTTOM_RIGHT || align === FLYOUT_ALIGN.TOP_RIGHT,
      }"
      :style="bodyOffsetStyle"
      @click="close"
    >
      <slot
        name="body"
        :close="close"
        :toggle="toggle"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import {
  useMouseInElement, onClickOutside, onKeyStroke, MaybeElementRef,
} from '@vueuse/core';
import {
  ref, watch, computed,
} from 'vue';
import {
  TFlyoutAlign, KEY_CODE, BODY_OFFSET, TBodyOffset, FLYOUT_ALIGN,
} from './flyout';

interface IProps {
  align?: TFlyoutAlign;
  bodyOffset?: TBodyOffset;
  detachFlyoutBody?: boolean;
  fullWidth?: boolean;
  ignoreElementsList?: (MaybeElementRef | string)[];
  canCloseOnClickOutside?: boolean;
}

const props = withDefaults(defineProps<IProps>(), {
  align: 'BOTTOM_CENTER',
  bodyOffset: BODY_OFFSET.SPACE_7,
  detachFlyoutBody: false,
  fullWidth: false,
  ignoreElementsList: () => [],
  canCloseOnClickOutside: true,
});

const flyoutBody = ref(null);

const isOpen = ref(false);

const flyoutRef = ref<null | HTMLDivElement>(null);

// eslint-disable-next-line func-call-spacing, no-spaced-func
defineEmits<{
  (event: 'toggle', value: boolean): void;
}>();

function open() {
  isOpen.value = true;
}

function close() {
  isOpen.value = false;
}

function toggle() {
  if (isOpen.value) {
    close();
  } else {
    open();
  }
}

const bodyOffsetStyle = computed(() => {
  switch (props.align) {
    case FLYOUT_ALIGN.BOTTOM_CENTER:
    case FLYOUT_ALIGN.BOTTOM_LEFT:
    case FLYOUT_ALIGN.BOTTOM_RIGHT:
      return {
        top: `calc(100% + ${props.bodyOffset})`,
      };
    case FLYOUT_ALIGN.TOP_CENTER:
    case FLYOUT_ALIGN.TOP_LEFT:
    case FLYOUT_ALIGN.TOP_RIGHT:
      return {
        bottom: `calc(100% + ${props.bodyOffset})`,
      };
    default:
      return {};
  }
});

onClickOutside(
  flyoutRef,
  () => {
    if (props.canCloseOnClickOutside) {
      close();
    }
  },
  {
    ignore: props.ignoreElementsList,
  },
);

const {
  isOutside,
} = useMouseInElement(flyoutBody);

onKeyStroke(KEY_CODE.ESCAPE, () => {
  close();
});

watch(isOutside, (newValue) => {
  if (newValue && props.canCloseOnClickOutside) {
    close();
  }
});
</script>
