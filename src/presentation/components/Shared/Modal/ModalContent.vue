<template>
  <transition
    name="modal-content-transition"
    @after-leave="onAfterTransitionLeave"
  >
    <div
      v-if="show"
      class="modal-content-wrapper"
    >
      <div
        ref="modalElement"
        class="modal-content-content"
        role="dialog"
        aria-expanded="true"
        aria-modal="true"
      >
        <slot />
      </div>
    </div>
  </transition>
</template>

<script lang="ts">
import { defineComponent, shallowRef } from 'vue';

export default defineComponent({
  props: {
    show: {
      type: Boolean,
      default: false,
    },
  },
  emits: [
    'transitionedOut',
  ],
  setup(_, { emit }) {
    const modalElement = shallowRef<HTMLElement | undefined>();

    function onAfterTransitionLeave() {
      emit('transitionedOut');
    }

    return {
      onAfterTransitionLeave,
      modalElement,
    };
  },
});

</script>

<style scoped lang="scss">
@use "@/presentation/assets/styles/main" as *;

$modal-content-transition-duration: 400ms;
$modal-content-color-shadow: $color-on-surface;
$modal-content-color-background: $color-surface;
$modal-content-offset-upward: $spacing-absolute-x-large;

@mixin scrollable() {
  overflow-y: auto;
  max-height: 100%;
}

.modal-content-wrapper {
  display: flex;
  align-items: center;
  justify-content: center;

  pointer-events: none;

  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  // Margin around modal content ensures visual comfort and consistency across devices.
  // It provides:
  // - A visually comfortable space preventing a claustrophobic feeling, especially on smaller screens.
  // - Consistent appearance on various screen sizes by using absolute spacing.
  // - Focus on the modal by dimming the background and emphasizing the task.
  // - Sufficient space on small devices for users to tap outside and close the modal.
  margin: $spacing-absolute-xx-large;
  @media screen and (max-width: $media-screen-small-width) {
    margin: $spacing-absolute-x-large; // Google and Apple recommend at least 44x44px for touch targets
  }
}

.modal-content-content {
  position: relative;
  overflow: hidden;
  box-sizing: border-box;

  pointer-events: auto;

  width: auto;
  height: auto;

  max-width: 600px;

  background-color: $color-surface;
  border-radius: 3px;
  box-shadow: 0 20px 60px -2px $color-on-surface;

  @include scrollable;
}

@include fade-slide-transition('modal-content-transition', $modal-content-transition-duration, $modal-content-offset-upward);
</style>
