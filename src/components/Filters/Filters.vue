<template lang="pug">
div(
  ref="containerRef",
  :class="filtersClassName",
  :style="searchFieldStyle",
)
  //- QueryField
  div(
    v-if="!hideQueryField",
    :class="styles.Container",
  )
    Box(padding="200")
      InlineStack(
        align="start",
        block-align="center",
        :gap="{ xs: '400', md: '300' }",
      )
        div(
          :class="styles.SearchField",
        )
          SearchField(
            v-model="modelValue",
            :placeholder="queryPlaceholder",
            :focused="focused",
            :loading="loading",
            :disabled="disabled || disableQueryField",
            :borderless-query-field="borderlessQueryField",
            :selected-view-name="selectedViewName",
            @clear="emits('query-clear')",
            @focus="emits('query-focus')",
            @blur="emits('query-blur')",
          )
        slot
  //- Filters
  FiltersBar(
    v-if="!hideFilterBar",
    :applied-filters="appliedFilters",
    :filters="filters",
    :disabled="disabled",
    :hide-query-field="hideQueryField",
    :disable-filters="disableFilters",
    :close-on-child-overlay-click="closeOnChildOverlayClick",
    :style="mountedStateStyles",
    @add-filter-click="emits('add-filter-click')",
    @clear-all="emits('clear-all')",
  )
    slot
</template>

<script setup lang="ts">
import { type VNode, computed } from 'vue';
import { classNames } from '@/utilities/css';
import type { AppliedFilterInterface, FilterInterface, VueNode } from '@/utilities/types';
import {
  InlineStack,
  Box,
} from '@/components';
import {
  FiltersBar,
  SearchField,
} from './components';
import styles  from '@polaris/components/Filters/Filters.module.scss';
import { type TransitionStatus } from '../IndexFilters/types';

const TRANSITION_DURATION = 'var(--p-motion-duration-150)';
const TRANSITION_MARGIN = '-36px';

const defaultStyle = {
  transition: `opacity ${TRANSITION_DURATION} var(--p-motion-ease)`,
  opacity: 0,
};

const transitionStyles = {
  entering: {opacity: 1},
  entered: {opacity: 1},
  exiting: {opacity: 0},
  exited: {opacity: 0},
  unmounted: {opacity: 0},
};

const defaultFilterStyles = {
  transition: `opacity ${TRANSITION_DURATION} var(--p-motion-ease), margin ${TRANSITION_DURATION} var(--p-motion-ease)`,
  opacity: 0,
  marginTop: TRANSITION_MARGIN,
};

const transitionFilterStyles = {
  entering: {
    opacity: 1,
    marginTop: 0,
  },
  entered: {
    opacity: 1,
    marginTop: 0,
  },
  exiting: {
    opacity: 0,
    marginTop: TRANSITION_MARGIN,
  },
  exited: {
    opacity: 0,
    marginTop: TRANSITION_MARGIN,
  },
  unmounted: {
    opacity: 0,
    marginTop: TRANSITION_MARGIN,
  },
};

export type FiltersProps = {
  /** Currently entered text in the query field */
  queryValue?: string;
  /** Placeholder text for the query field. */
  queryPlaceholder?: string;
  /** Whether the query field is focused. */
  focused?: boolean;
  /** Available filters added to the filter bar. Shortcut filters are pinned to the front of the bar. */
  filters: FilterInterface[];
  /** Applied filters which are rendered as filter pills. The remove callback is called with the respective key. */
  appliedFilters?: AppliedFilterInterface[];
  /** Disable all filters. */
  disabled?: boolean;
  /** Hide filter bar for applied filters. */
  hideFilters?: boolean;
  /** Hide the query field. */
  hideQueryField?: boolean;
  /** Disable the query field. */
  disableQueryField?: boolean;
  /** Disable the filters */
  disableFilters?: boolean;
  /** Whether the text field should be borderless. Should be true when used as part of the IndexFilters component. */
  borderlessQueryField?: boolean;
  /** Whether an asyncronous task is currently being run. */
  loading?: boolean;
  mountedState?: TransitionStatus;
  /** Whether the filter should close when clicking inside another Popover. */
  closeOnChildOverlayClick?: boolean;
  /** @deprecated The name of the currently selected view */
  selectedViewName?: string;
};

type FiltersEvents = {
  /** Callback when the query field is changed. */
  'query-change': [query: string];
  /** Callback when the clear button is triggered. */
  'query-clear': [];
  /** Callback when the reset all button is pressed. */
  'clear-all': [];
  /** Callback when the query field is blurred. */
  'query-blur': [];
  /** Callback when the query field is focused. */
  'query-focus': [];
  /** Callback when the add filter button is clicked. */
  'add-filter-click': [];
};

const props = defineProps<FiltersProps>();
const emits = defineEmits<FiltersEvents>();
const slots = defineSlots<{
  /** The content to display inline with the controls. */
  default?: (_?: VueNode) => VNode[];
}>();

const modelValue = computed({
  get: () => props.queryValue || '',
  set: (value: string) => emits('query-change', value),
});

const filtersClassName = computed(() => classNames(
  styles.Filters,
  props.hideQueryField && styles.hideQueryField,
));
const searchFieldStyle = computed(() => {
  if (props.mountedState === 'entered') {
    return undefined;
  }

  return props.mountedState
    ? {
        ...defaultStyle,
        ...transitionStyles[props.mountedState],
      }
    : undefined;
});
const mountedStateStyles = computed(() => {
  return props.mountedState && !props.hideQueryField
    ? {
        ...defaultFilterStyles,
        ...transitionFilterStyles[props.mountedState],
      }
    : undefined;
});
const hideFilterBar = computed(() => props.hideFilters || props.filters.length === 0);
</script>
