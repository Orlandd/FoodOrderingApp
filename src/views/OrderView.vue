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
                  <button class="btn btn-success">Order</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- item list -->

      <!-- ordered item -->
      <div class="col-12 col-sm-4 mb-3">das</div>
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
  },
};
</script>
<style lang=""></style>
