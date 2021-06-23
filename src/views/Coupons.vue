<template>
  <Loading :active="isLoading"></Loading>
  <div class="text-end">
    <button type="button" class="btn btn-primary" @click="openModal(true)">新增產品</button>
  </div>
  <table class="table mt-4">
    <thead>
      <tr>
        <th>名稱</th>
        <th>折扣碼</th>
        <th>折扣百分比</th>
        <th>到期日</th>
        <th>是否啟用</th>
        <th>編輯</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in coupons" :key="item.id">
        <td>{{ item.title }}</td>
        <td>{{ item.code }}</td>
        <td class="text-right">{{ item.percent }}%</td>
        <td class="text-right">
          {{ $filter.date(item.due_date) }}
        </td>
        <td>
          <span class="text-success" v-if="item.is_enabled">已啟用</span>
          <span class="text-secondary" v-else>未啟用</span>
        </td>
        <td>
          <div class="btn-group">
            <button class="btn btn-outline-primary btn-sm" @click="openModal(false, item)">
              編輯
            </button>
            <button class="btn btn-outline-danger btn-sm" @click="delItem(item)">
              刪除
            </button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <CouponModal ref="couponModal" :coupon="tempCoupon" @handle-coupon="updateCoupon" />
  <DelModal ref="delModal" :product="tempCoupon" @check-del="delCoupon" />
</template>

<script>
import CouponModal from '@/components/CouponModal.vue';
import DelModal from '@/components/DelModal.vue';

export default {
  data() {
    return {
      coupons: [],
      isLoading: false,
      tempCoupon: {
        title: '',
        is_enabled: 1,
        percent: 100,
        code: '',
      },
      isNew: false,
    };
  },
  components: {
    CouponModal,
    DelModal,
  },
  methods: {
    getCoupons() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupons`;
      this.axios.get(api).then((res) => {
        this.coupons = res.data.coupons;
        this.isLoading = false;
      });
    },
    openModal(isNew, item) {
      this.isNew = isNew;

      if (isNew) {
        this.tempCoupon = {
          due_date: new Date().getTime() / 1000,
          is_enabled: 1,
        };
      } else {
        this.tempCoupon = { ...item };
      }

      const modal = this.$refs.couponModal;
      modal.showModal();
    },
    updateCoupon(item) {
      this.tempCoupon = item;
      this.isLoading = true;

      // 新增
      let api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon`;
      let httpMethod = 'post';

      // 修改
      if (!this.isNew) {
        api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${item.id}`;
        httpMethod = 'put';
      }

      this.axios[httpMethod](api, { data: this.tempCoupon }).then((res) => {
        console.log(res);
        this.coupons = res.data.coupons;
        this.$refs.couponModal.hideModal();
        this.getCoupons();
        this.isLoading = false;
      });
    },
    delItem(item) {
      this.$refs.delModal.showModal();
      this.tempCoupon = item;
    },
    delCoupon(item) {
      const api = `${process.env.VUE_APP_API}api/${process.env.VUE_APP_PATH}/admin/coupon/${item.id}`;
      this.axios.delete(api).then((res) => {
        console.log(res);
        if (res.data.success) {
          this.$refs.delModal.hideModal();
          this.getCoupons();
        }
      });
    },
  },
  created() {
    this.getCoupons();
  },
};
</script>
