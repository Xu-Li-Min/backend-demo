<template>
  <Loading :active="isLoading"></Loading>
  <table class="table mt-4">
    <thead>
      <tr>
        <th>購買時間</th>
        <th>Email</th>
        <th>購買款項</th>
        <th>應付金額</th>
        <th>是否付款</th>
        <th>編輯</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in orders" :key="item.create_at">
        <td>{{ $filter.date(item.create_at) }}</td>
        <td>{{ item.user.email }}</td>
        <td class="text-right">
          <template v-for="item in item.products" :key="item">
            <p>
              {{ item.product.title }}
              <span style="font-size: 14px">(數量：{{ item.qty }}{{ item.product.unit }})</span>
            </p>
          </template>
        </td>
        <td class="text-right">{{ item.total }}</td>
        <td>
          <div class="form-check form-switch">
            <input
              class="form-check-input"
              type="checkbox"
              :id="`paidSwitch${item.id}`"
              v-model="item.is_paid"
              @change="updatePaid(item)"
            />
            <label class="form-check-label" :for="`paidSwitch${item.id}`">
              <span v-if="item.is_paid">已付款</span>
              <span v-else>未付款</span>
            </label>
          </div>
          <!-- <span class="text-success" v-if="item.is_paid">已付款</span>
          <span class="text-secondary" v-else>未付款</span> -->
        </td>
        <td>
          <div class="btn-group">
            <button class="btn btn-outline-primary btn-sm" @click="modalOpen(item)">
              檢視
            </button>
            <button class="btn btn-outline-danger btn-sm" @click="delOrder(item)">
              刪除
            </button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <OrderModal ref="orderModal" :orderInfo="tempOrder" @confirm-order="getOrders"></OrderModal>
</template>

<script>
import OrderModal from '@/components/OrderModal.vue';

export default {
  data() {
    return {
      orders: [],
      tempOrder: {},
      isLoading: false,
    };
  },
  components: {
    OrderModal,
  },
  methods: {
    getOrders() {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/orders`;

      this.axios.get(api).then((res) => {
        this.orders = res.data.orders;
      });
    },
    updatePaid(item) {
      this.isLoading = true;
      const orderId = item.id;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${orderId}`;
      console.log(item, api);
      this.axios.put(api, { data: item }).then((res) => {
        console.log(res);
        this.isLoading = false;
      });
    },
    delOrder(item) {
      this.isLoading = true;
      const orderId = item.id;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/order/${orderId}`;
      this.axios.delete(api).then((res) => {
        console.log(res);
        if (res.data.success) {
          this.getOrders();
        }
        this.isLoading = false;
      });
    },
    modalOpen(item) {
      this.tempOrder = { ...item };
      console.log(item);
      const modal = this.$refs.orderModal;
      modal.showModal();
    },
  },
  created() {
    this.getOrders();
  },
};
</script>
