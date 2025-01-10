<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container">
      <table class="table table-bordered mt-5">
        <tbody>
          <tr>
            <td>Customer : {{ order.customer_name }}</td>
            <td>Table No : {{ order.table_no }}</td>
            <td>Order Date : {{ order.order_date }}</td>
            <td>Order Time : {{ order.order_time }}</td>
          </tr>
          <tr>
            <td>Waitresss : {{ order.waitress?.name }}</td>
            <td>Cashier : {{ order.cashier ? order.cashier.name : "-" }}</td>
            <td>Status : {{ order.status }}</td>
            <td>Total : {{ formatToIDR(order.total) }}</td>
          </tr>
        </tbody>
      </table>

      <table class="table mt-5">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Item Name</th>
            <th scope="col">Price</th>
            <th scope="col">Quantity</th>
            <th scope="col">Total</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in order.order_detail" :key="index">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ item.item.name }}</td>
            <td>{{ formatToIDR(item.price) }}</td>
            <td>{{ item.quantity }}</td>
            <td>{{ formatToIDR(item.quantity * item.price) }}</td>
          </tr>
        </tbody> 
      </table>

      <!-- done if user is chef and order status == ordered -->
      <!-- paid if user cashier or manager order staus == done -->


        <div class="d-flex  gap-2">
            <button class="btn btn-primary" v-if="(order.status == 'ordered') && (roleId == 2 )" @click="updateStatus('done')">Done</button>
            <button class="btn btn-success" v-if="(order.status == 'done') && (roleId == 1 || roleId == 4)" @click="updateStatus('paid')">Paid</button>

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
