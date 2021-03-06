<script>
import Icon from '~/vue_shared/components/icon.vue';
import Tooltip from '~/vue_shared/directives/tooltip';
import { GlLoadingIcon } from '@gitlab/ui';

export default {
  name: 'Badge',
  components: {
    Icon,
    Tooltip,
    GlLoadingIcon,
  },
  directives: {
    Tooltip,
  },
  props: {
    imageUrl: {
      type: String,
      required: true,
    },
    linkUrl: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      hasError: false,
      isLoading: true,
      numRetries: 0,
    };
  },
  computed: {
    imageUrlWithRetries() {
      if (this.numRetries === 0) {
        return this.imageUrl;
      }

      return `${this.imageUrl}#retries=${this.numRetries}`;
    },
  },
  watch: {
    imageUrl() {
      this.hasError = false;
      this.isLoading = true;
      this.numRetries = 0;
    },
  },
  methods: {
    onError() {
      this.isLoading = false;
      this.hasError = true;
    },
    onLoad() {
      this.isLoading = false;
    },
    reloadImage() {
      this.hasError = false;
      this.isLoading = true;
      this.numRetries += 1;
    },
  },
};
</script>

<template>
  <div>
    <a
      v-show="!isLoading && !hasError"
      :href="linkUrl"
      target="_blank"
      rel="noopener noreferrer"
    >
      <img
        :src="imageUrlWithRetries"
        class="project-badge"
        aria-hidden="true"
        @load="onLoad"
        @error="onError"
      />
    </a>

    <gl-loading-icon
      v-show="isLoading"
      :inline="true"
    />

    <div
      v-show="hasError"
      class="btn-group"
    >
      <div class="btn btn-default btn-sm disabled">
        <icon
          :size="16"
          class="prepend-left-8 append-right-8"
          name="doc-image"
          aria-hidden="true"
        />
      </div>
      <div
        class="btn btn-default btn-sm disabled"
      >
        <span class="prepend-left-8 append-right-8">{{ s__('Badges|No badge image') }}</span>
      </div>
    </div>

    <button
      v-show="hasError"
      v-tooltip
      :title="s__('Badges|Reload badge image')"
      class="btn btn-transparent btn-sm text-primary"
      type="button"
      @click="reloadImage"
    >
      <icon
        :size="16"
        name="retry"
      />
    </button>
  </div>
</template>
