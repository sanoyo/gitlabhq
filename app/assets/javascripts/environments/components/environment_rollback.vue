<script>
/**
 * Renders Rollback or Re deploy button in environments table depending
 * of the provided property `isLastDeployment`.
 *
 * Makes a post request when the button is clicked.
 */
import { s__ } from '~/locale';
import { GlTooltipDirective, GlLoadingIcon } from '@gitlab/ui';
import Icon from '~/vue_shared/components/icon.vue';
import eventHub from '../event_hub';

export default {
  components: {
    Icon,
    GlLoadingIcon,
  },
  directives: {
    GlTooltip: GlTooltipDirective,
  },
  props: {
    retryUrl: {
      type: String,
      default: '',
    },

    isLastDeployment: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      isLoading: false,
    };
  },

  computed: {
    title() {
      return this.isLastDeployment
        ? s__('Environments|Re-deploy to environment')
        : s__('Environments|Rollback environment');
    },
  },

  methods: {
    onClick() {
      this.isLoading = true;

      eventHub.$emit('postAction', { endpoint: this.retryUrl });
    },
  },
};
</script>
<template>
  <button
    v-gl-tooltip
    :disabled="isLoading"
    :title="title"
    type="button"
    class="btn d-none d-sm-none d-md-block"
    @click="onClick"
  >
    <icon
      v-if="isLastDeployment"
      name="repeat"
    />
    <icon
      v-else
      name="redo"
    />
    <gl-loading-icon v-if="isLoading" />
  </button>
</template>
