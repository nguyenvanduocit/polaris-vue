<template lang="pug">
div(
  v-if="message",
  :class="styles.InlineError",
  :id="errorTextID(fieldId)",
)
  div(:class="styles.Icon")
    Icon(:source="AlertCircleIcon")
  template(v-if="typeof message === 'string'")
    span {{ message }}
  component(v-else, :is="message")
</template>

<script setup lang="ts">
import { Icon } from '@/components';
import type { Error } from '@/utilities/types';
import { errorTextID } from './utils';
import AlertCircleIcon from '@icons/AlertCircleIcon.svg';
import styles from '@polaris/components/InlineError/InlineError.module.scss';

interface InlineErrorProps {
  /** Content briefly explaining how to resolve the invalid form field input. */
  message: Error;
  /** Unique identifier of the invalid form field that the message describes */
  fieldId: string;
}

defineProps<InlineErrorProps>();
</script>
