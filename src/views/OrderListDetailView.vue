<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container ">
      <!-- <div class="relative overflow-x-auto shadow-md sm:rounded-lg mb-5 w-full">
        <table class="w-full text-sm text-left  text-gray-500 dark:text-gray-400">
          <tbody>
            <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
              <td  class="px-6 py-3">Customer : {{ order.customer_name }}</td>
              <td  class="px-6 py-3">Table No : {{ order.table_no }}</td>
              <td  class="px-6 py-3">Order Date : {{ order.order_date }}</td>
              <td  class="px-6 py-3">Order Time : {{ order.order_time }}</td>
            </tr>
            <tr class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600">
              <td  class="px-6 py-3">Waitresss : {{ order.waitress?.name }}</td>
              <td  class="px-6 py-3">Cashier : {{ order.cashier ? order.cashier.name : "-" }}</td>
              <td  class="px-6 py-3">Status : {{ order.status }}</td>
              <td  class="px-6 py-3">Total : {{ formatToIDR(order.total) }}</td>
            </tr>
          </tbody>
        </table>
      </div> -->

      <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-4 px-4 m-5">
        <div>Customer : {{ order.customer_name }}</div>
        <div>Table No : {{ order.table_no }}</div>
        <div>Order Date : {{ order.order_date }}</div>
        <div>Order Time : {{ order.order_time }}</div>
        <div>Waitresss : {{ order.waitress?.name }}</div>
        <div>Cashier : {{ order.cashier ? order.cashier.name : "-" }}</div>
        <div>Status : {{ order.status }}</div>
        <div>Total : {{ formatToIDR(order.total) }}</div>
      </div>


      <div class="relative overflow-x-auto shadow-md sm:rounded-lg">
        <table
          class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400"
        >
          <thead
            class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
          >
            <tr>
              <th class="px-6 py-3" scope="col">#</th>
              <th class="px-6 py-3" scope="col">Item Name</th>
              <th class="px-6 py-3" scope="col">Price</th>
              <th class="px-6 py-3" scope="col">Quantity</th>
              <th class="px-6 py-3" scope="col">Total</th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(item, index) in order.order_detail"
              :key="index"
              class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
            >
              <th class="px-6 py-3" scope="row">{{ index + 1 }}</th>
              <td class="px-6 py-3">{{ item.item.name }}</td>
              <td class="px-6 py-3">{{ formatToIDR(item.price) }}</td>
              <td class="px-6 py-3">{{ item.quantity }}</td>
              <td class="px-6 py-3">
                {{ formatToIDR(item.quantity * item.price) }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- done if user is chef and order status == ordered -->
      <!-- paid if user cashier or manager order staus == done -->

      <div class="d-flex gap-2">
        <button
          class="px-4 py-2 bg-cyan-300 rounded-md"
          v-if="order.status == 'ordered' && roleId == 2"
          @click="updateStatus('done')"
        >
          Done
        </button>
        <button
          class="px-4 py-2 bg-lime-300 rounded-md"
          v-if="order.status == 'done' && (roleId == 1 || roleId == 4)"
          @click="updateStatus('paid')"
        >
          Paid
        </button>
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
      order: [],
      total: 0,
      customerName: "",
      tableNo: "",
      processing: false,
      orderId: "",
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
        .get(`http://foodorder.test/api/order/${this.orderId}`, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response.data.data);
          this.order = response.data.data;
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
    updateStatus(status) {
      if (status == "done") {
        axios
          .get(`http://foodorder.test/api/order/${this.orderId}/set-as-done`, {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          })
          .then((response) => {
            console.log(response.data.data);
            this.order = response.data.data;

            // this.order.status = response.data.data.status;
            // this.order.update_at = response.data.data.update_at;
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
      }
      if (status == "paid") {
        axios
          .get(`http://foodorder.test/api/order/${this.orderId}/payment`, {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          })
          .then((response) => {
            console.log(response.data.data);
            this.order = response.data.data;
            // this.order.status = response.data.data.status;
            // this.order.update_at = response.data.data.update_at;
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
      }
    },
  },
};
</script>
<style lang=""></style>
