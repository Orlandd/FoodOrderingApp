<template lang="">
  <NavBar :name="username" :role="roleId" />

  <div class="container">
    <div class="p-3">
      <input
        type="text"
        name=""
        id=""
        v-model="search"
        :onchange="searchItem()"
        class="border-1 px-4 py-2 w-full rounded-full"
        placeholder="Search for food"
      />
    </div>
  </div>

  <div class="container px-4">
    <div
      class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 w-full mb-4"
    >
      <div
        class="grid grid-cols-2 md:grid-cols-2 lg:grid-cols-4 gap-2 lg:gap-4 lg:col-span-2"
      >
        <div v-for="(item, index) in filteredItems" :key="index">
          <div
            class="p-2 border-1 shadow-md rounded-md hover:shadow-lime-300 hover:scale-105 transition-all"
          >
            <div class="aspect-square bg-slate-400 rounded-md overflow-hidden">
              <img
                v-if="item.image"
                :src="baseUrlImage + item.image"
                class="object-cover w-full h-full"
                alt="Image"
              />
              <img
                v-else="!item.image"
                src="@/assets/images/noImage.png"
                class="object-cover w-full h-full"
                alt="Image"
              />
            </div>
            <div class="">
              <h3 class="text-lg">{{ item.name }}</h3>
              <div class="flex justify-between mt-2">
                <p class="font-bold">{{ formatToIDR(item.price) }}</p>
                <button
                  class="px-3 bg-lime-200 rounded-full text-sm hover:bg-lime-400 transition-all"
                  @click="orderItem(item.id)"
                >
                  Order
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="w-full">
        <h2 class="text-lg font-semibold">Order List</h2>
        <hr class="my-3" />
        <div class="">
          <label for="customerName" class="w-full mb-1"> Customer Name </label>
          <input
            type="text"
            class="px-2 border-1 rounded-md py-2 w-full"
            id="customerName"
            v-model="customerName"
          />
          <label for="tableNo" class="w-full mb-1"> Table No </label>
          <input
            type="text"
            class="px-2 border-1 rounded-md py-2 w-full"
            id="tableNo"
            v-model="tableNo"
          />
        </div>
        <div
          class="w-full flex justify-between items-center px-2 border-1 rounded-md py-2 my-2"
          v-for="(order, index) in orders"
          :key="index"
        >
          <div class="">
            <h3 class="text-lg">{{ order.name }}</h3>
            <p class="font-bold">@{{ formatToIDR(order.eachPrice) }}</p>
            <div class="">
              <button
                class="px-3 text-sm bg-yellow-300 rounded-full hover:bg-yellow-400 transition-all"
                @click="quantityDecrease(order.id)"
              >
                -
              </button>
              <span class="px-3">{{ order.quantity }}</span>
              <button
                class="px-3 text-sm bg-lime-300 rounded-full hover:bg-lime-400 transition-all"
                @click="quantityIncrease(order.id)"
              >
                +
              </button>
              <button
                class="px-3 text-sm bg-red-300 rounded-full ms-3 hover:bg-red-400 transition-all"
                @click="deleteItem(order.id)"
              >
                x
              </button>
            </div>
          </div>
          <div class="">
            <span class="font-bold">{{ formatToIDR(order.price) }}</span>
          </div>
        </div>
        <hr class="my-3" />
        <div class="">
          <div class="flex justify-between items-center">
            <h2 class="text-lg font-semibold">Total Price</h2>
            <h2 class="text-lg font-semibold">{{ formatToIDR(total) }}</h2>
          </div>
          <button
            class="w-full bg-lime-200 py-2 rounded-md text-center font-bold mt-3 hover:bg-lime-400 transition-all"
            x-bind:disabled="processing"
            @click="submitOrder()"
          >
            {{ processing ? "Processing..." : "Submit Order" }}
          </button>
        </div>
      </div>
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
