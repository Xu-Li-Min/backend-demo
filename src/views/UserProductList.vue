<template>
  <table class="table align-middle text-center">
    <thead>
      <tr>
        <th scope="col">#</th>
        <th scope="col">產品名稱</th>
        <th scope="col">產品分類</th>
        <th scope="col">產品圖片</th>
        <th scope="col"></th>
      </tr>
    </thead>
    <tbody v-for="(item, index) in productList" :key="index">
      <tr>
        <th scope="row">{{ index + 1 }}</th>
        <td>{{ item.title }}</td>
        <td>{{ item.category }}</td>
        <td>
          <img :src="item.imageUrl" style="width: 200px" alt="" />
        </td>
        <td>
          <button
            type="button"
            class="btn btn-secondary me-3"
            @click="getProduct(item.id)"
          >
            商品詳細
          </button>
          <button
            type="button"
            class="btn btn-danger position-relative"
            @click="addCart(item.id)"
            :disabled="item.id === status.loadingItem"
          >
            <div
              class="position-absolute start-50 top-50 translate-middle"
              v-if="item.id === status.loadingItem"
            >
              <div class="spinner-border spinner-border-sm" role="status">
                <span class="visually-hidden">Loading...</span>
              </div>
            </div>
            加入購物車
          </button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  data() {
    return {
      productList: [],
      status: {
        loadingItem: '',
      },
    };
  },
  methods: {
    getProductList() {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/products/all`;
      this.axios.get(api).then((res) => {
        this.productList = res.data.products;
      });
    },
    getProduct(id) {
      this.$router.push(`/user/product/${id}`);
    },
    addCart(id) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      this.status.loadingItem = id;
      const cart = {
        product_id: id,
        qty: 1,
      };
      this.axios.post(api, { data: cart }).then((res) => {
        console.log(res);
        this.status.loadingItem = '';
      });
    },
  },
  mounted() {
    this.getProductList();
  },
};
</script>
