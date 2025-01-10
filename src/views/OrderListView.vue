<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container mt-5">
      <h2>Order List</h2>
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
            <th scope="col">Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(order, index) in orders">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ order.customer_name }}</td>
            <td>{{ order.table_no }}</td>
            <td>{{ order.order_date }}</td>
            <td>{{ order.order_time }}</td>
            <td>{{ order.total }}</td>
            <td>{{ order.status }}</td>
            <td>{{ order.waitress.name }}</td>
            <td>{{ order.cashier ? order.cashier.name : 'Not Assigned' }}</td>
            <td>
              <RouterLink
                :to="{ name: 'orderListDetail', params: { id: order.id } }"
                class="badge bg-primary text-decoration-none"
              >
                Detail
              </RouterLink>
            </td>
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
    }
  }
};
</script>
<style lang=""></style>
