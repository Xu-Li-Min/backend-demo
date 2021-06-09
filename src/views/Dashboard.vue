<template>
  <Navbar></Navbar>
  <ToastMsg></ToastMsg>
  <div class="container-fluid">
    <router-view />
  </div>
</template>

<script>
import emitter from '@/methods/emitter';
import ToastMsg from '@/components/ToastMsg.vue';
import Navbar from '../components/Navbar.vue';

export default {
  components: { Navbar, ToastMsg },
  provide() {
    return {
      emitter,
    };
  },
  created() {
    const token = document.cookie.replace(
      /(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/,
      // eslint-disable-next-line comma-dangle
      '$1'
    );
    this.$http.defaults.headers.common.Authorization = token;
    const api = `${process.env.VUE_APP_API}api/user/check`;
    this.$http.post(api).then((res) => {
      if (!res.data.success) {
        this.$router.push('/login');
      }
      // console.log('dash', res);
    });
  },
};
</script>
