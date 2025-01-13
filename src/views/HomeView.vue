<template lang="">
  <NavBar :name=username :role=roleId />
  <div>
    <div class="container px-4">
      <h6 class="text-xl font-semibold">Hi, {{ username }}</h6>

    </div>
  </div>
</template>

<script>
import router from '@/router';
import NavBar from '@/components/NavBar.vue';

export default {
  components: {
    NavBar
  },
  data() {
    return {
      username: "",
      roleId: "",
    }
  },
  mounted() {
    this.username = localStorage.getItem('name');
    this.roleId = localStorage.getItem("role_id");

    if (!this.username || this.username == "" || this.username == null) {
      router.push({ name: 'login' });
    }

  }
}
</script>
<style lang="">

</style>