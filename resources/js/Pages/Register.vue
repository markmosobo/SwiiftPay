<template>
    <div class="container">
      <div class="login-section">
        <div class="logo">
          <img :src="getPhoto() + '/paxful.png'" alt="Paxful Logo" />
          paxful
        </div>
        <div class="back-button">&lt; <a style="color:white;" href="/">Back</a></div>
        <div class="progress-bar">
          <div class="step active"></div>
          <div class="step"></div>
          <div class="step"></div>
          <div class="step"></div>
          <div class="step"></div>
        </div>
        <div class="welcome-text">Create an account</div>
        <div class="signup-link">Already have an account? <a href="#">Sign in</a></div>
        <form @submit.prevent="submitRegister">
          <div class="input2">
              <label for="email">Email/Phone Number</label>
              <input type="text" v-model="form.email" id="email" placeholder="Email/Phone Number" required>
          </div>
          <button type="submit">Next</button>
        </form>
      </div>
      <div class="illustration-section">
        <div class="illustration">
          <img :src="getPhoto() + '/registersvg.PNG'" alt="Illustration" />
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
        return {
          form: {
            email: "",
          },
        };
    },
    methods: {
      getPhoto() {
        return "/storage/images/";
      },
      async submitRegister() {
          this.errorMessage = "";
    
          try {
            const response = await axios.post("/api/register", this.form);
            console.log(response)
            if (response.data.status == 'success') {
              this.$router.push('/login')
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
  
  <style> 
  </style>
  