<template>
    <div class="flex justify-center items-center min-h-screen bg-gray-100">
        <div class="w-96 bg-white shadow-md rounded-lg p-6">
            <h5 class="text-center text-2xl font-semibold mb-6">Login</h5>
            <form @submit.prevent="login">
                <div class="mb-4">
                    <label for="email" class="block text-sm font-medium text-gray-700">Email address</label>
                    <input type="email" id="email" v-model="email" placeholder="Enter your email" required
                        class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500" />
                </div>
                <div class="mb-4">
                    <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
                    <input type="password" id="password" v-model="password" placeholder="Enter your password" required
                        class="mt-1 block w-full px-4 py-2 border border-gray-300 rounded-md shadow-sm focus:ring-blue-500 focus:border-blue-500" />
                </div>
                <div class="flex items-center mb-4">
                    <input type="checkbox" id="rememberMe"
                        class="h-4 w-4 text-blue-600 focus:ring-blue-500 border-gray-300 rounded">
                    <label for="rememberMe" class="ml-2 block text-sm text-gray-900">Remember me</label>
                </div>
                <button type="submit"
                    class="w-full bg-cyan-300 text-white py-2 px-4 rounded-md hover:bg-cyan-700 focus:outline-none focus:ring-2 focus:ring-blue-500 focus:ring-offset-2">
                    Login
                </button>
            </form>
            <div class="mt-4 text-center">
                <small class="text-gray-600">Don't have an account? <a href="#"
                        class="text-cyan-500 hover:underline">Sign up</a></small>
            </div>
        </div>
    </div>

</template>

<script>
import axios from "axios";
import router from '@/router';

export default {
    data() {
        return {
            email: '',
            password: ''
        };
    },
    methods: {
        login() {
            axios
                .post('http://foodorder.test/api/auth/login', {
                    email: this.email,
                    password: this.password
                })
                .then((response) => {
                    console.log(response);

                    // Save user data to localStorage
                    localStorage.setItem('email', response.data.data.email);
                    localStorage.setItem('name', response.data.data.name);
                    localStorage.setItem('role_id', response.data.data.role_id);
                    localStorage.setItem('token', response.data.data.token);

                    // Navigate to home page
                    router.push({ name: 'home' });
                })
                .catch((error) => {
                    // Handle errors
                    if (error.response && error.response.data) {
                        alert(error.response.data.message);
                    } else {
                        console.log('Error:', error.message);
                    }
                });
        }
    },
    mounted() {
        const username = localStorage.getItem('name');
        if (username) {
            router.push({ name: 'home' });
        }
    }
};
</script>

<style>
.vh-100 {
    height: 100vh;
}

.card {
    border-radius: 15px;
}
</style>