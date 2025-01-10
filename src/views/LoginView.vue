<template>
    <div>
        <div class="container d-flex justify-content-center align-items-center vh-100">
            <div class="card shadow-sm" style="width: 24rem;">
                <div class="card-body">
                    <h5 class="card-title text-center mb-4">Login</h5>
                    <form @submit.prevent="login">
                        <div class="mb-3">
                            <label for="email" class="form-label">Email address</label>
                            <input type="email" v-model="email" class="form-control" id="email"
                                placeholder="Enter your email" required />
                        </div>
                        <div class="mb-3">
                            <label for="password" class="form-label">Password</label>
                            <input type="password" v-model="password" class="form-control" id="password"
                                placeholder="Enter your password" required />
                        </div>
                        <div class="mb-3 form-check">
                            <input type="checkbox" class="form-check-input" id="rememberMe" />
                            <label class="form-check-label" for="rememberMe">Remember me</label>
                        </div>
                        <button type="submit" class="btn btn-primary w-100">Login</button>
                    </form>
                    <div class="mt-3 text-center">
                        <small>Don't have an account? <a href="#">Sign up</a></small>
                    </div>
                </div>
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