<template lang="">
  <NavBar :name="username" :role="roleId" />

  <div class="container-fluid mt-5">
    <div class="row">
      <!-- item list -->
      <div class="col-12 col-sm-8 mb-3">
        <!-- search -->
        <div class="col-12">
          <input
            type="text"
            name=""
            id=""
            v-model="search"
            :onchange="searchItem()"
            class="form-control"
            placeholder="Search for food"
          />
        </div>
        <hr />
        <!-- item -->
        <div class="col-12 mb-3">
          <div class="row">
            <div
              v-for="(item, index) in filteredItems"
              :key="index"
              class="col-12 col-md-6 col-lg-3"
            >
              <div class="card">
                <img
                  :src="baseUrlImage + item.image"
                  height="250px"
                  class="card-img-top object-fit-cover"
                  alt="..."
                />
                <div class="card-body text-center">
                  <h5 class="card-title">{{ item.name }}</h5>
                  <p class="card-text">{{ item.price }}</p>
                  <button class="btn btn-success" @click="orderItem(item.id)">
                    Order
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- item list -->

      <!-- ordered item -->
      <div class="col-12 col-sm-4 mb-3">
        <h2>Order List</h2>
        <hr />
        <div class="my-3">
          <div class="">
            <label for="customerName" class="form-label"> Customer Name </label>
            <input
              type="text"
              class="form-control"
              id="customerName"
              v-model="customerName"
            />
            <label for="tableNo" class="form-label"> Table No </label>
            <input
              type="text"
              class="form-control"
              id="tableNo"
              v-model="tableNo"
            />
          </div>
        </div>
        <hr />
        <div class="">
          <div class="mb-3" v-for="(order, index) in orders" :key="index">
            <div class="d-flex justify-content-between fs-5">
              <span>{{ order.name }}</span>
              <span>{{ formatToIDR(order.price) }}</span>
            </div>
            <div class="d-flex justify-content-between fs-5">
              <span class="fs-6 fw-light"
                >@{{ formatToIDR(order.eachPrice) }}</span
              >
              <div class="">
                <button
                  class="btn btn-sm btn-warning"
                  @click="quantityDecrease(order.id)"
                >
                  -
                </button>
                <span class="mx-4">{{ order.quantity }}</span>
                <button
                  class="btn btn-sm btn-success"
                  @click="quantityIncrease(order.id)"
                >
                  +
                </button>
                <button
                  class="btn btn-sm btn-danger ms-3"
                  @click="deleteItem(order.id)"
                >
                  delete
                </button>
              </div>
            </div>
          </div>
        </div>
        <hr />
        <div class="">
          <div class="d-flex justify-content-between fs-5 mb-3 fw-bold">
            <span>Total</span>
            <span>{{ formatToIDR(total) }}</span>
          </div>
          <button
            class="btn btn-primary w-100"
            x-bind:disabled="processing"
            @click="submitOrder()"
          >
            {{ processing ? "Processing..." : "Submit Order" }}
          </button>
        </div>
      </div>
      <!-- ordered item -->
    </div>
  </div>
</template>
<script>
import NavBar from "@/components/NavBar.vue";
import axios from "axios";

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

    if (this.roleId != 4) {
      router.push({ name: "home" });
    }

    this.getItem();
  },
  methods: {
    formatToIDR(value) {
      return new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR",
        minimumFractionDigits: 0, // Bisa diubah sesuai kebutuhan
      }).format(value);
    },
    getItem() {
      axios
        .get("http://foodorder.test/api/item", {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response.data.data);
          this.items = response.data.data;
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
    searchItem() {
      this.filteredItems = this.items.filter((item) => {
        return item.name.toLowerCase().includes(this.search.toLowerCase());
      });
    },
    orderItem(id) {
      let item = this.filteredItems.filter((item) => item.id == id)[0];
      let order = Object.assign({}, item);

      let indexOfArrayItem = this.orders.map((e) => e.id).indexOf(order.id);
      order.eachPrice = order.price;

      if (indexOfArrayItem != -1) {
        this.orders[indexOfArrayItem].quantity++;
        // this.orders[indexOfArrayItem].eachPrice = order.price;
        this.orders[indexOfArrayItem].price =
          this.orders[indexOfArrayItem].quantity * order.price;
      } else {
        order.quantity = 1;
        this.orders.push(order);
      }

      let total = this.orders.map((e) => e.price).reduce((a, b) => a + b, 0);

      this.total = total;
    },
    quantityDecrease(id) {
      let item = this.orders.filter((item) => item.id == id)[0];

      if (item.quantity > 1) {
        item.quantity--;
        item.price = item.quantity * item.eachPrice;
        this.total = this.orders.map((e) => e.price).reduce((a, b) => a + b, 0);
      } else {
        let indexOfArrayItem = this.orders.map((e) => e.id).indexOf(item.id);
        this.orders.splice(indexOfArrayItem, 1);
        this.total = this.orders.map((e) => e.price).reduce((a, b) => a + b, 0);
      }
    },
    quantityIncrease(id) {
      let item = this.orders.filter((item) => item.id == id)[0];

      item.quantity++;
      item.price = item.quantity * item.eachPrice;

      this.total = this.orders.map((e) => e.price).reduce((a, b) => a + b, 0);
    },
    deleteItem(id) {
      let item = this.orders.filter((item) => item.id == id)[0];

      let indexOfArrayItem = this.orders.map((e) => e.id).indexOf(item.id);
      this.orders.splice(indexOfArrayItem, 1);
      this.total = this.orders.map((e) => e.price).reduce((a, b) => a + b, 0);
    },
    submitOrder() {
      if (this.customerName == "" || this.tableNo == "") {
        alert("Customer Name and Table No must be filled");
        return;
      }

      this.processing = true;

      console.log(this.orders);

      let item = this.orders.map((item) => {
        return {
          id: item.id,
          quantity: item.quantity,
        };
      });

      axios
        .post(
          "http://foodorder.test/api/order",
          {
            customer_name: this.customerName,
            table_no: this.tableNo,
            items: item,
          },
          {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          }
        )
        .then((response) => {
          console.log(response);
          this.orders = [];
          this.total = 0;
          this.customerName = "";
          this.tableNo = "";
          alert("Order Success");
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => {
          this.processing = false;
        });
    },
  },
};
</script>
<style lang=""></style>
