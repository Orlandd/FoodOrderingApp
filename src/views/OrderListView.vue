<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container px-4 mt-5">
      <h2 class="font-medium text-xl text-center my-6">Order List</h2>
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
              <th class="px-6 py-3" scope="col">Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(order, index) in orders" class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
              <th class="px-6 py-3" scope="row">{{ index + 1 }}</th>
              <td class="px-6 py-3">{{ order.customer_name }}</td>
              <td class="px-6 py-3">{{ order.table_no }}</td>
              <td class="px-6 py-3">{{ order.order_date }}</td>
              <td class="px-6 py-3">{{ order.order_time }}</td>
              <td class="px-6 py-3">{{ order.total }}</td>
              <td class="px-6 py-3">{{ order.status }}</td>
              <td class="px-6 py-3">{{ order.waitress.name }}</td>
              <td class="px-6 py-3">{{ order.cashier ? order.cashier.name : "Not Assigned" }}</td>
              <td class="px-6 py-3">
                <RouterLink
                  :to="{ name: 'orderListDetail', params: { id: order.id } }"
                  class="px-3 py-1 bg-cyan-300 rounded-full font-semibold text-slate-900"
                >
                  Detail
                </RouterLink>
              </td>
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
      orders: [],
      total: 0,
      customerName: "",
      tableNo: "",
      processing: false,
    };
  },
  mounted() {
    // INI HARUS DICEK KE DATABASE ATAU ENKRIPSI

    this.username = localStorage.getItem("name");
    this.roleId = localStorage.getItem("role_id");
    if (!this.username || this.username == "" || this.username == null) {
      router.push({ name: "login" });
    }

    this.getData();
  },
  methods: {
    getData() {
      axios
        .get(`http://foodorder.test/api/order`, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response.data.data);
          this.orders = response.data.data;
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
