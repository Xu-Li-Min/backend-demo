<template>
  <div
    class="toast"
    role="alert"
    aria-live="assertive"
    aria-atomic="true"
    ref="toast"
  >
    <div class="toast-header">
      <span :class="`bg-${msg.style}`" class="rounded me-2"></span>
      <strong class="me-auto">{{ msg.title }}</strong>
      <button
        type="button"
        class="btn-close"
        data-bs-dismiss="toast"
        aria-label="Close"
      ></button>
    </div>
    <div class="toast-body">{{ msg.content }}</div>
  </div>
</template>

<script>
import Toast from 'bootstrap/js/dist/toast';

export default {
  name: 'Toast',
  props: ['msg'],
  data() {
    return {};
  },
  mounted() {
    const toastEl = this.$refs.toast;
    const toast = new Toast(toastEl, {
      delay: 5000,
    });
    toast.show();
  },
};
</script>
