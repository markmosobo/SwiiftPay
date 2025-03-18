<template>
    <div class="container">
        <div class="login-section">
            <div class="logo">
                <img :src="getPhoto() + '/paxful.png'" alt="Paxful Logo">
                paxful
            </div>
            <div class="welcome-text">Welcome back!</div>
            <div class="signup-link">Don't have an account? <a href="/register" style="text-decoration: underline;">Sign up</a></div>
            <form @submit.prevent="submitLogin">

                <div class="input2">
                    <label for="email">Email/Phone Number</label>
                    <input type="text" v-model="form.email" id="email" placeholder="Email/Phone Number" required>
                </div>
                <label for="password">Password</label>
                <div class="password-wrapper">
                <input :type="showPassword ? 'text' : 'password'" v-model="form.password" id="password" placeholder="Password" required>
                <i class="fa fa-eye" :class="showPassword ? 'fa-eye-slash' : 'fa-eye'" @click="togglePassword"></i>
                </div>

                <div class="forgot-password"><a href="#" style="text-decoration: underline;">Forgot password?</a></div>
                <button type="submit">Sign in</button>
            </form>
        </div>
        <div class="illustration-section">
            <div class="illustration">
                <img :src="getPhoto() + '/svg.PNG'" alt="Illustration">
            </div>
        </div>
    </div>
</template>
  
<script>
import axios from "axios";

export default {
data() {
return {
  form: {
    email: "",
    password: ""
  },
  showPassword: false,
};
},
methods: {
getPhoto()
{
    return "/storage/images/";
},
togglePassword() {
  this.showPassword = !this.showPassword;
},
async submitLogin() {
  this.errorMessage = "";

  try {
    const response = await axios.post("/api/login", this.form);
    
    if (response.data.status == 'success') {
        this.$router.push({ path: '/mfa', query: { email: this.form.email } });
    } else {
      this.errorMessage = response.data.message || "Invalid credentials.";
    }
  } catch (error) {
    this.errorMessage = "Login failed. Please check your credentials.";
  }

  this.loading = false;
}
},
};
</script>

