<template lang="">
  <div>
    <NavBar :name="username" :role="roleId" />

    <div class="container">
      <div class="my-5">
        <h1 class="font-semibold text-2xl mb-3">Product List</h1>

        <RouterLink
          to="/product/add"
          class="px-4 py-2 bg-lime-200 rounded-full my-3"
        >
          Add Product
        </RouterLink>

        <div class="relative overflow-x-auto shadow-md sm:rounded-lg my-5">
          <table
            class="w-full text-sm text-left rtl:text-right text-gray-500 dark:text-gray-400"
          >
            <thead
              class="text-xs text-gray-700 uppercase bg-gray-50 dark:bg-gray-700 dark:text-gray-400"
            >
              <tr>
                <th scope="col" class="px-6 py-3">#</th>
                <th scope="col" class="px-6 py-3">Product Name</th>
                <th scope="col" class="px-6 py-3">Price</th>
                <th scope="col" class="px-6 py-3">Image</th>
                <th scope="col" class="px-6 py-3">Action</th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, index) in items"
                class="bg-white border-b dark:bg-gray-800 dark:border-gray-700 hover:bg-gray-50 dark:hover:bg-gray-600"
              >
                <td
                  class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                >
                  {{ index + 1 }}
                </td>
                <td
                  class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                >
                  {{ item.name }}
                </td>
                <td
                  class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                >
                  {{ item.price }}
                </td>
                <td
                  class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                >
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
                <td
                  class="px-6 py-4 font-medium text-gray-900 whitespace-nowrap dark:text-white"
                >
                  <RouterLink
                    :to="{ name: 'productUpdate', params: { id: item.id } }"
                    class="px-4 py-2 bg-yellow-200 rounded-full"
                    >Update</RouterLink
                  >
                </td>
              </tr>
            </tbody>
          </table>
        </div>
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
