<script>
import axios from 'axios'
import { RouterLink, RouterView } from 'vue-router'
import router from '@/router';
export default {
  data() {
    return {
      username: localStorage.getItem('name')
    }
  },
  methods: {
    logout() {
      axios.get('http://foodorder.test/api/auth/logout', {
        headers: {
          Authorization: 'Bearer ' + localStorage.getItem('token')
        }
      })
        .then(function (response) {
          console.log(response);
          localStorage.removeItem('email');
          localStorage.removeItem('name');
          localStorage.removeItem('role_id');
          localStorage.removeItem('token');
          router.push({ name: 'login' });

        })
        .catch(function (error) {
          console.log(error);
        });
    }
  }
}
</script>

<template>



  <!-- <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/about">About</RouterLink>
      </nav> -->


  <RouterView />
</template>

<style scoped></style>
