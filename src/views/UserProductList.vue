<template>
  <div class="row">
    <div class="col-6">
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
    </div>

    <!-- 購物車 -->
    <div class="col-6">
      購物車
      <table class="table align-middle text-center">
        <thead>
          <tr>
            <th scope="col">品名</th>
            <th scope="col" style="width: 150px">數量</th>
            <th scope="col">單價</th>
            <th scope="col"></th>
          </tr>
        </thead>
        <tbody>
          <template v-if="cart">
            <tr v-for="item in cart.carts" :key="item.id">
              <th scope="row">
                {{ item.product.title }}
                <div class="text-success">已套用優惠券</div>
              </th>
              <td>
                <div class="input-group input-group-sm">
                  <input
                    type="number"
                    class="form-control"
                    v-model.number="item.qty"
                    @change="updateCart(item)"
                    min="1"
                    :disabled="item.id === status.loadingItem"
                  />
                  <div class="input-group-text">/ {{ item.qty }}個</div>
                </div>
              </td>

              <td>{{ item.total }}</td>
              <td>
                <button
                  type="button"
                  class="btn btn-danger position-relative"
                  @click="delCartItem(item.id)"
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
                  <i class="bi bi-x"></i>
                </button>
              </td>
            </tr>
          </template>
        </tbody>
        <tfoot>
          <tr>
            <td colspan="3" class="text-end">總計</td>
            <td class="text-center">{{ cart.total }}</td>
          </tr>
          <tr>
            <td colspan="3" class="text-end text-success">折扣價</td>
            <td class="text-center text-success">0</td>
          </tr>
        </tfoot>
      </table>
      <div class="input-group mb-3 input-group-sm">
        <input type="text" class="form-control" placeholder="請輸入優惠碼" />
        <div class="input-group-append">
          <button class="btn btn-outline-secondary" type="button">
            套用優惠碼
          </button>
        </div>
      </div>
    </div>
  </div>
  <div class="my-5 row justify-content-center">
    <Form class="col-md-6" v-slot="{ errors }" @submit="createOrder">
      <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <Field
          id="email"
          name="email"
          type="email"
          class="form-control"
          :class="{ 'is-invalid': errors['email'] }"
          placeholder="請輸入 Email"
          rules="email|required"
          v-model="form.user.email"
        ></Field>
        <ErrorMessage name="email" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="name" class="form-label">收件人姓名</label>
        <Field
          id="name"
          name="姓名"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': errors['姓名'] }"
          placeholder="請輸入姓名"
          rules="required"
          v-model="form.user.name"
        ></Field>
        <ErrorMessage name="姓名" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="tel" class="form-label">收件人電話</label>
        <Field
          id="tel"
          name="電話"
          type="tel"
          class="form-control"
          :class="{ 'is-invalid': errors['電話'] }"
          placeholder="請輸入電話"
          rules="required"
          v-model="form.user.tel"
        ></Field>
        <ErrorMessage name="電話" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="address" class="form-label">收件人地址</label>
        <Field
          id="address"
          name="地址"
          type="text"
          class="form-control"
          :class="{ 'is-invalid': errors['地址'] }"
          placeholder="請輸入地址"
          rules="required"
          v-model="form.user.address"
        ></Field>
        <ErrorMessage name="地址" class="invalid-feedback"></ErrorMessage>
      </div>

      <div class="mb-3">
        <label for="message" class="form-label">留言</label>
        <textarea
          name=""
          id="message"
          class="form-control"
          cols="30"
          rows="10"
          v-model="form.message"
        ></textarea>
      </div>
      <div class="text-end">
        <button class="btn btn-danger">送出訂單</button>
      </div>
    </Form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      productList: [],
      status: {
        loadingItem: '',
      },
      cart: [],
      form: {
        user: {
          name: '',
          email: '',
          tel: '',
          address: '',
        },
        message: '',
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
        this.getCart();
      });
    },
    getCart() {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart`;
      this.axios.get(api).then((res) => {
        this.cart = res.data.data;
        console.log(this.cart);
      });
    },
    delCartItem(id) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${id}`;
      this.status.loadingItem = id;

      this.axios.delete(api).then((res) => {
        console.log(res);
        this.getCart();
        this.status.loadingItem = '';
      });
    },
    updateCart(item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/cart/${item.id}`;
      this.status.loadingItem = item.id;
      const productData = {
        product_id: item.id,
        qty: item.qty,
      };
      this.axios.put(api, { data: productData }).then((res) => {
        console.log(res);
        this.status.loadingItem = '';
        this.getCart();
      });
    },
    createOrder() {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/order`;
      const orderData = this.form;
      this.axios.post(api, { data: orderData }).then((res) => {
        console.log(res);
      });
    },
  },
  mounted() {
    this.getProductList();
    this.getCart();
  },
};
</script>
