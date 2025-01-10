<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container mt-5">
      <h2 class="text-center mb-4">Add Product</h2>
      <form @submit.prevent="addItem()" enctype="multipart/form-data">
        <div class="mb-3">
          <label for="name" class="form-label">Product Name</label>
          <input
            type="text"
            class="form-control"
            id="name"
            v-model="name"
            placeholder="Enter product name"
            required
          />
        </div>
        <div class="mb-3">
          <label for="price" class="form-label">Price</label>
          <input
            type="number"
            class="form-control"
            id="price"
            v-model="price"
            placeholder="Enter product price"
            required
          />
        </div>
        <div class="mb-3">
          <label for="image" class="form-label">Product Image</label>
          <input
            type="file"
            class="form-control"
            id="image"
            @change="imageChanged($event)"
            accept="image/*"
          />
        </div>
        <button type="submit" class="btn btn-primary w-100">Submit</button>
      </form>
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
      username: "",
      roleId: "",
      name: "",
      image: "",
      price: "",
      file: "",
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
  },
  methods: {
    addItem() {
      let data = {
        name: this.name,
        price: this.price,
      };

      let formData = new FormData();
      formData.append("image_file", this.file);
      formData.append("name", this.name);
      formData.append("price", this.price);

      axios
        .post("http://127.0.0.1:8000/api/item", formData, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
            "Content-Type": "multipart/form-data",
          },
        })
        .then((response) => {
          console.log(response);
          router.push({ name: "product" });
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
    imageChanged(e) {
      let file = e.target.files[0];
      this.file = file;
    },
  },
};
</script>
<style lang=""></style>
