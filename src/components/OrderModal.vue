<template>
  <div
    class="modal fade"
    id="orderModal"
    tabindex="-1"
    role="dialog"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
    ref="modal"
  >
    <div class="modal-dialog modal-xl" role="document">
      <div class="modal-content border-0">
        <div class="modal-header bg-dark text-white">
          <h5 class="modal-title" id="exampleModalLabel">
            <span>訂單細節</span>
          </h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="row">
            <div class="col-md-4">
              <h3>用戶資料</h3>
              <table class="table">
                <tbody v-if="tempOrder.user">
                  <tr>
                    <th style="width: 100px;">姓名</th>
                    <td>{{ tempOrder.user.name }}</td>
                  </tr>
                  <tr>
                    <th>Email</th>
                    <td>{{ tempOrder.user.email }}</td>
                  </tr>
                  <tr>
                    <th>電話</th>
                    <td>{{ tempOrder.user.tel }}</td>
                  </tr>
                  <tr>
                    <th>地址</th>
                    <td>{{ tempOrder.user.address }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
            <div class="col-md-8">
              <h3>訂單細節</h3>
              <table class="table">
                <tbody>
                  <tr>
                    <th style="width: 100px">訂單編號</th>
                    <td>{{ tempOrder.id }}</td>
                  </tr>
                  <tr>
                    <th>下單時間</th>
                    <td>{{ $filter.date(tempOrder.create_at) }}</td>
                  </tr>
                  <tr>
                    <th>付款時間</th>
                    <td>
                      <span v-if="tempOrder.paid_date">
                        {{ $filter.date(tempOrder.paid_date) }}
                      </span>
                      <span v-else></span>
                    </td>
                  </tr>
                  <tr>
                    <th>付款狀態</th>
                    <td>
                      <strong class="text-success" v-if="tempOrder.is_paid">已付款</strong>
                      <span class="text-muted" v-else>尚未付款</span>
                    </td>
                  </tr>
                  <tr>
                    <th>總金額</th>
                    <td>{{ $filter.currency(tempOrder.total) }}</td>
                  </tr>
                </tbody>
              </table>
              <h3>選購商品</h3>
              <table class="table">
                <thead>
                  <tr></tr>
                </thead>
                <tbody>
                  <tr v-for="item in tempOrder.products" :key="item.id">
                    <th>{{ item.product.title }}</th>
                    <td>{{ item.qty }} / {{ item.product.unit }}</td>
                    <td class="text-end">{{ $filter.currency(item.final_total) }}</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
        <div class="modal-footer">
          <!-- <button type="button" class="btn btn-outline-secondary" data-bs-dismiss="modal">
            取消
          </button> -->
          <button type="button" class="btn btn-primary" data-bs-dismiss="modal">
            確認
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import modalMixin from '@/mixins/modalMixin';

export default {
  props: {
    orderInfo: {
      type: Object,
      default() {
        return {};
      },
    },
  },
  data() {
    return {
      modal: '',
      tempOrder: {},
      isPaid: false,
    };
  },
  watch: {
    orderInfo() {
      this.tempOrder = this.orderInfo;
      this.isPaid = this.tempOrder.is_paid;
    },
  },
  mixins: [modalMixin],
  methods: {
    confirm() {},
  },
};
</script>
