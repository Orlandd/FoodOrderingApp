<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container px-4 mt-5">
      <div class="mb-3 w-50">
        <label for="month" class="font-semibold mb-1 w-full">Month</label>
        <select
          class="rounded-full border-1 px-2  py-2 w-full"
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

      <!-- <div class="col-12 mt-5">
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
            <div class="bg-lime-400 rounded-2 p-2">
              <h4>Max Payment</h4>
              <p class="fw-bold fs-4">{{ reports.max_payment }}</p>
            </div>
          </div>
        </div>
      </div> -->
      <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
        <div class="p-4 border-3 rounded-lg border-lime-300">
          <h4 class="text-2xl">Order Count</h4>
          <p class="fw-bold fs-4">{{ reports.order_count }}</p>
        </div>
        <div class="p-4 border-3 rounded-lg border-lime-300">
          <h4 class="text-2xl">Min Payment</h4>
          <p class="fw-bold fs-4">{{ formatToIDR(reports.min_payment) }}</p>
        </div>
        <div class="p-4 border-3 rounded-lg border-lime-300">
          <h4 class="text-2xl">Max Payment</h4>
          <p class="fw-bold fs-4">{{ formatToIDR(reports.max_payment) }}</p>
        </div>
      </div>

      <hr class="my-5">
      <div class="relative overflow-x-auto shadow-md sm:rounded-lg mb-5">
        <table class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400">
          <thead class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400">
            <tr>
              <th class="px-6 py-3" scope="col">#</th>
              <th class="px-6 py-3" scope="col">Customer Name</th>
              <th class="px-6 py-3" scope="col">Table No</th>
              <th class="px-6 py-3" scope="col">Order Date</th>
              <th class="px-6 py-3" scope="col">Time</th>
              <th class="px-6 py-3" scope="col">Total</th>
              <th class="px-6 py-3" scope="col">Status</th>
              <th class="px-6 py-3" scope="col">Waitress</th>
              <th class="px-6 py-3" scope="col">Cashier</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(report, index) in reports.orders" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
              <th class="px-6 py-3" scope="row">{{ index + 1 }}</th>
              <td class="px-6 py-3">{{ report.customer_name }}</td>
              <td class="px-6 py-3">{{ report.table_no }}</td>
              <td class="px-6 py-3">{{ report.order_date }}</td>
              <td class="px-6 py-3">{{ report.order_time }}</td>
              <td class="px-6 py-3">{{ formatToIDR(report.total) }}</td>
              <td class="px-6 py-3">{{ report.status }}</td>
              <td class="px-6 py-3">{{ report.waitress?.name }}</td>
              <td class="px-6 py-3">{{ report.cashier ? report.cashier.name : 'Not Assigned' }}</td>
            </tr>
          </tbody>
        </table>
      </div>
      
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";
import router from "@/router";
import { initFlowbite } from 'flowbite'

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
    initFlowbite();

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
    formatToIDR(value) {
      return new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR",
        minimumFractionDigits: 0, // Bisa diubah sesuai kebutuhan
      }).format(value);
    },
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
