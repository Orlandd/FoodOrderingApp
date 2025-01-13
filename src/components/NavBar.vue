<template lang="">
  <header v-if="$route.name !== 'login'">
    <nav class="bg-white border-gray-200 dark:bg-gray-900">
      <div
        class="max-w-screen-xl flex flex-wrap items-center justify-between mx-auto p-4"
      >
        <a href="#" class="flex items-center space-x-3 rtl:space-x-reverse">
          <span class="text-2xl font-semibold text-gray-800 dark:text-white"
            >Brand</span
          >
        </a>
        <button
          data-collapse-toggle="navbarSupportedContent"
          type="button"
          class="inline-flex items-center p-2 w-10 h-10 justify-center text-sm text-gray-500 rounded-lg lg:hidden hover:bg-gray-100 focus:outline-none focus:ring-2 focus:ring-gray-200 dark:text-gray-400 dark:hover:bg-gray-700 dark:focus:ring-gray-600"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
        >
          <span class="sr-only">Open main menu</span>
          <svg
            class="w-6 h-6"
            aria-hidden="true"
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
          >
            <path
              stroke="currentColor"
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 12h16M4 18h16"
            />
          </svg>
        </button>
        <div
          id="navbarSupportedContent"
          class="hidden sm:mt-3 lg:mt-0 w-full lg:flex lg:w-auto"
        >
          <ul
            class="font-medium flex flex-col lg:p-0 rounded-lg lg:flex-row lg:space-x-8 rtl:space-x-reverse lg:mt-0 lg:border-0 lg:bg-white dark:bg-gray-800 lg:dark:bg-gray-900 dark:border-gray-700"
          >
            <li>
              <RouterLink
                to="/"
                :class="['block py-2 px-3  rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0  lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent',
                  $route.path === '/' ? 'text-lime-300 lg:dark:text-lime-500'
                  : 'text-gray-900 dark:text-white',
                ]"
                >Home</RouterLink
              >
            </li>
            <li v-if="role == 1 || role == 4">
              <RouterLink
                to="/order"
               
                :class="['block py-2 px-3  rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0  lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent',
                  $route.path === '/order' ? 'text-lime-300 lg:dark:text-lime-500'
                  : 'text-gray-900 dark:text-white',
                ]"
                >Order</RouterLink
              >
            </li>
            <li>
              <RouterLink
                to="/order/list"
                :class="['block py-2 px-3  rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0  lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent',
                  $route.path === '/order/list' ? 'text-lime-300 lg:dark:text-lime-500'
                  : 'text-gray-900 dark:text-white',
                ]"
                >Order List</RouterLink
              >
            </li>
            <li v-if="role == 4">
              <RouterLink
                to="/order/report"
                :class="['block py-2 px-3  rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0  lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent',
                  $route.path === '/order/report' ? 'text-lime-300 lg:dark:text-lime-500'
                  : 'text-gray-900 dark:text-white',
                ]"
                >Order Report</RouterLink
              >
            </li>
            <li v-if="role == 1 || role == 4">
              <RouterLink
                to="/product"
                :class="['block py-2 px-3  rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0  lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent',
                  $route.path === '/product' ? 'text-lime-300 lg:dark:text-lime-500'
                  : 'text-gray-900 dark:text-white',
                ]"
                >Product</RouterLink
              >
            </li>
            <!-- <li>
              <a
                href="#"
                @click="logout"
                class="block py-2 px-3 text-gray-900 rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0 dark:text-white lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent"
                >Logout</a
              >
            </li> -->
          </ul>
          <div class="flex items-center">
            <button
              id="dropdownDefaultButton"
              data-dropdown-toggle="dropdown"
              class="text-black bg-lime-300 hover:bg-lime-500 focus:ring-2 focus:outline-none focus:ring-lime-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center inline-flex items-center dark:bg-lime-600 dark:hover:bg-lime-700 dark:focus:ring-lime-800 dark:text-white"
              type="button"
            >
              {{ name }}
              <svg
                class="w-2.5 h-2.5 ms-3"
                aria-hidden="true"
                xmlns="http://www.w3.org/2000/svg"
                fill="none"
                viewBox="0 0 10 6"
              >
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="m1 1 4 4 4-4"
                />
              </svg>
            </button>

            <!-- Dropdown menu -->
            <div
              id="dropdown"
              class="z-10 hidden bg-white divide-y divide-gray-100 rounded-lg shadow w-44 dark:bg-gray-700"
            >
              <ul
                class="py-2 text-sm text-gray-700 dark:text-gray-200"
                aria-labelledby="dropdownDefaultButton"
              >
                <li>
                  <a
                    href="#"
                    @click="logout"
                    class="block py-2 px-3 text-gray-900 rounded hover:bg-gray-100 lg:hover:bg-transparent lg:border-0 lg:hover:text-lime-300 lg:p-0 dark:text-white lg:dark:hover:text-lime-500 dark:hover:bg-gray-700 dark:hover:text-white lg:dark:hover:bg-transparent"
                    >Logout</a
                  >
                </li>
              </ul>
            </div>

            <!-- <span class="text-gray-800 dark:text-white">Hi, {{ name }}</span> -->
          </div>
        </div>
      </div>
    </nav>

    <!-- 
    <nav class="navbar navbar-expand-lg bg-body-tertiary">
      <div class="container-fluid">
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav me-auto mb-2 mb-lg-0">
            <li class="nav-item me-3">
              <RouterLink to="/">Home</RouterLink>
            </li>
            <li class="nav-item me-3" v-if="role == 1 || role == 4">
              <RouterLink to="/order">Order</RouterLink>
            </li>
            <li class="nav-item me-3">
              <RouterLink to="/order/list">Order List</RouterLink>
            </li>
            <li class="nav-item me-3" v-if="role == 4">
              <RouterLink to="/order/report">Order Report</RouterLink>
            </li>
            <li class="nav-item me-3" v-if="role == 1 || role == 4">
              <RouterLink to="/product">Product</RouterLink>
            </li>
            <li class="nav-item">
              <a href="#" @click="logout()">Logout</a>
            </li>
          </ul>
          <li class="d-flex" role="search">
            <span>Hi, {{ name }}</span>
          </li>
        </div>
      </div>
    </nav> -->
  </header>
</template>
<script>
import axios from "axios";
import router from "@/router";

export default {
  props: ["name", "role"],
  methods: {
    logout() {
      axios
        .get("http://foodorder.test/api/auth/logout", {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then(function (response) {
          console.log(response);
          localStorage.removeItem("email");
          localStorage.removeItem("name");
          localStorage.removeItem("role_id");
          localStorage.removeItem("token");
          router.push({ name: "login" });
        })
        .catch(function (error) {
          console.log(error);
        });
    },
  },
};
</script>
<style lang=""></style>
