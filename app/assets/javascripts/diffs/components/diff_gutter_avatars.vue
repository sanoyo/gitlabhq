<script>
import { mapActions } from 'vuex';
import Icon from '~/vue_shared/components/icon.vue';
import tooltip from '~/vue_shared/directives/tooltip';
import { pluralize, truncate } from '~/lib/utils/text_utility';
import UserAvatarImage from '~/vue_shared/components/user_avatar/user_avatar_image.vue';
import { COUNT_OF_AVATARS_IN_GUTTER, LENGTH_OF_AVATAR_TOOLTIP } from '../constants';

export default {
  directives: {
    tooltip,
  },
  components: {
    Icon,
    UserAvatarImage,
  },
  props: {
    discussions: {
      type: Array,
      required: true,
    },
  },
  computed: {
    discussionsExpanded() {
      return this.discussions.every(discussion => discussion.expanded);
    },
    allDiscussions() {
      return this.discussions.reduce((acc, note) => acc.concat(note.notes), []);
    },
    notesInGutter() {
      return this.allDiscussions.slice(0, COUNT_OF_AVATARS_IN_GUTTER).map(n => ({
        note: n.note,
        author: n.author,
      }));
    },
    moreCount() {
      return this.allDiscussions.length - this.notesInGutter.length;
    },
    moreText() {
      if (this.moreCount === 0) {
        return '';
      }

      return pluralize(`${this.moreCount} more comment`, this.moreCount);
    },
  },
  methods: {
    ...mapActions(['toggleDiscussion']),
    getTooltipText(noteData) {
      let note = noteData.note;

      if (note.length > LENGTH_OF_AVATAR_TOOLTIP) {
        note = truncate(note, LENGTH_OF_AVATAR_TOOLTIP);
      }

      return `${noteData.author.name}: ${note}`;
    },
    toggleDiscussions() {
      this.discussions.forEach(discussion => {
        this.toggleDiscussion({
          discussionId: discussion.id,
        });
      });
    },
  },
};
</script>

<template>
  <div class="diff-comment-avatar-holders">
    <button
      v-if="discussionsExpanded"
      @click="toggleDiscussions"
      type="button"
      aria-label="Show comments"
      class="diff-notes-collapse js-diff-comment-avatar js-diff-comment-button"
    >
      <icon
        name="collapse"
        :size="12"
      />
    </button>
    <template v-else>
      <user-avatar-image
        v-for="note in notesInGutter"
        :key="note.id"
        @click.native="toggleDiscussions"
        class="diff-comment-avatar js-diff-comment-avatar"
        :img-src="note.author.avatar_url"
        :tooltip-text="getTooltipText(note)"
        :size="19"
      />
      <span
        v-if="moreText"
        v-tooltip
        :title="moreText"
        @click="toggleDiscussions"
        class="diff-comments-more-count has-tooltip js-diff-comment-avatar js-diff-comment-plus"
        data-container="body"
        data-placement="top"
        role="button"
      >+{{ moreCount }}</span>
    </template>
  </div>
</template>