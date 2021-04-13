<template>
  <transition
    name="fade"
  >
    <div
      v-if="!hidden"
      class="ui message"
      :class="type"
    >
      <i
        class="close icon"
        @click="hide"
      />
      <div class="header">
        {{ header }}
      </div>
      <slot />
    </div>
  </transition>
</template>

<script>
export default {
  name: 'NotificationMessage',
  props: {
    type: {
      type: String,
      default: 'info',
    },
    header: {
      type: String,
      default: 'Info',
    },
  },
  data() {
    return {
      hidden: false,
    };
  },
  methods: {
    hide() {
      this.hidden = true;
    },
  },
};
</script>

<style scoped>
.fade-enter-active {
transition: fadeIn .3s ease;
}

.fade-leave-active {
 transition: opacity 0;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    opacity: 1;
  }
}
</style>
