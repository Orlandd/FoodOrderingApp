<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container">
      <div class="my-5">
        <h1>Product List</h1>

        <RouterLink to="/product/add" class="btn btn-success">
          Add Product
        </RouterLink>

        <table class="table table-striped">
          <thead>
            <tr>
              <th>#</th>
              <th>Product Name</th>
              <th>Price</th>
              <th>Image</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in items">
              <td>{{ index + 1 }}</td>
              <td>{{ item.name }}</td>
              <td>{{ item.price }}</td>
              <td>
                <img
                  v-if="item.image"
                  :src="baseUrlImage + item.image"
                  width="50px"
                  alt=""
                />
                <img
                  v-else="!item.image"
                  src="@/assets/images/noImage.png"
                  width="50px"
                  alt=""
                />
              </td>
              <td>
                <RouterLink
                  :to="{ name: 'productUpdate', params: { id: item.id } }"
                  class="btn btn-warning"
                  >Update</RouterLink
                >
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
import router from "@/router";
import axios from "axios";

export default {
  components: {
    NavBar,
  },
  data() {
    return {
      baseUrlImage: "http://foodorder.test/storage/items/",
      username: "",
      roleId: "",
      index: 0,
      items: [],
    };
  },
  mounted() {
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
          if (error.response.status == 401) {
            localStorage.removeItem("email");
            localStorage.removeItem("name");
            localStorage.removeItem("role_id");
            localStorage.removeItem("token");
            router.push({ name: "login" });
          }
          console.log(error);
        });
    },
  },
};
</script>
<style lang=""></style>
