<template>
  <div class="container">
    <form class="form-signin" @submit.prevent="this.signin">
      <h1 class="h3 mb-3 font-weight-normal">請先登入</h1>
      <div class="form-group">
        <label for="inputEmail" class="sr-only">Email address</label>
        <input
          id="inputEmail"
          type="email"
          class="form-control"
          placeholder="Email address"
          required
          autofocus
          v-model="user.username"
        />
      </div>
      <div class="form-group">
        <label for="inputPassword" class="sr-only">Password</label>
        <input
          id="inputPassword"
          type="password"
          class="form-control"
          placeholder="Password"
          required
          v-model="user.password"
        />
      </div>
      <button class="btn btn-lg btn-primary btn-block" type="submit">
        登入
      </button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      user: {
        username: '',
        password: '',
      },
    };
  },
  methods: {
    signin() {
      const api = `${process.env.VUE_APP_API}admin/signin`;
      this.$http.post(api, this.user).then((res) => {
        const { token, expired } = res.data;
        document.cookie = `hexToken=${token}; expires=${new Date(expired)}`;
        this.$router.push('/dashboard/products');
        // console.log('login', res);
      });
    },
  },
};
</script>
