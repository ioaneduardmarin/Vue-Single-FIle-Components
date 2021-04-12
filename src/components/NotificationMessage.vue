<template>
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
