<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container mt-5">
      <div class="mb-3 w-50">
        <label for="month" class="form-label">Month</label>
        <select
          class="form-select form-select-sm"
          aria-label="Small select example"
          id="month"
          v-model="month"
          @change="getData()"
        >
          <option value="">Choose Month</option>
          <option v-for="month in months" :value="month.value">
            {{ month.name }}
          </option>
        </select>
      </div>

      <div class="col-12 mt-5">
        <div class="row ">
          <div class="col-12 col-sm-4 ">
            <div class="bg-primary-subtle rounded-2 p-2">
              <h4>Order Count</h4>
              <p class="fw-bold fs-4">{{ reports.order_count }}</p>
            </div>
          </div>
          <div class="col-12 col-sm-4 ">
            <div class="bg-primary-subtle rounded-2 p-2">
              <h4>Min Payment</h4>
              <p class="fw-bold fs-4">{{ reports.min_payment }}</p>
            </div>
          </div>
          <div class="col-12 col-sm-4 ">
            <div class="bg-primary-subtle rounded-2 p-2">
              <h4>Max Payment</h4>
              <p class="fw-bold fs-4">{{ reports.max_payment }}</p>
            </div>
          </div>
        </div>
      </div>

      <hr>
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Customer Name</th>
            <th scope="col">Table No</th>
            <th scope="col">Order Date</th>
            <th scope="col">Time</th>
            <th scope="col">Total</th>
            <th scope="col">Status</th>
            <th scope="col">Waitress</th>
            <th scope="col">Cashier</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(report, index) in reports.orders">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ report.customer_name }}</td>
            <td>{{ report.table_no }}</td>
            <td>{{ report.order_date }}</td>
            <td>{{ report.order_time }}</td>
            <td>{{ report.total }}</td>
            <td>{{ report.status }}</td>
            <td>{{ report.waitress?.name }}</td>
            <td>{{ report.cashier ? report.cashier.name : 'Not Assigned' }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";
import router from "@/router";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      baseUrlImage: "http://foodorder.test/storage/items/",
      username: "",
      items: [],
      search: "",
      roleId: "",
      filteredItems: [],
      reports: [],
      total: 0,
      customerName: "",
      tableNo: "",
      processing: false,
      orderId: "",
      months: [
        { value: 1, name: "January" },
        { value: 2, name: "February" },
        { value: 3, name: "March" },
        { value: 4, name: "April" },
        { value: 5, name: "May" },
        { value: 6, name: "June" },
        { value: 7, name: "July" },
        { value: 8, name: "August" },
        { value: 9, name: "September" },
        { value: 10, name: "October" },
        { value: 11, name: "November" },
        { value: 12, name: "December" },
      ],
      month: "",
    };
  },
  mounted() {
    // INI HARUS DICEK KE DATABASE ATAU ENKRIPSI

    this.orderId = this.$route.params.id;
    this.username = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");
    if (!this.username || this.username == "" || this.username == null) {
      router.push({ name: "login" });
    }

    if (this.roleId != 4) {
      router.push({ name: "home" });
    }

    this.getData();
  },
  methods: {
    getData() {
      axios
        .get(`http://foodorder.test/api/order-report?month=${this.month}`, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response.data.data);
          this.reports = response.data.data;
        })
        .catch((error) => {
          console.log(error);
          if (error.response.status == 401) {
            localStorage.removeItem("email");
            localStorage.removeItem("name");
            localStorage.removeItem("role_id");
            localStorage.removeItem("token");
            router.push({ name: "login" });
          }
        });
    },
  },
};
</script>
<style lang=""></style>
