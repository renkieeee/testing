<template>
  <div class="register-page">
    <div class="register-card">
      <img src="../assets/logo1.png" alt="Logo" class="logo" />

      <h2>Creating Account</h2>
      <p class="subtitle">View your growth and connect!</p>

      <div class="form-group">
        <label>First Name</label>
        <input
          type="text"
          placeholder="Enter First Name"
          v-model="firstName"
        />
      </div>

      <div class="form-group">
        <label>Last Name</label>
        <input
          type="text"
          placeholder="Enter Last Name"
          v-model="lastName"
        />
      </div>

      <div class="form-group">
        <label>Security Code</label>
        <div class="password-wrapper">
          <input
            :type="showSecurity ? 'text' : 'password'"
            placeholder="Enter Security Code"
            v-model="securityCode"
          />
          <span class="toggle" @click="showSecurity = !showSecurity">👁</span>
        </div>
      </div>

      <div class="form-group">
        <label>Password</label>
        <div class="password-wrapper">
          <input
            :type="showPassword ? 'text' : 'password'"
            placeholder="Enter your Password"
            v-model="password"
          />
          <span class="toggle" @click="showPassword = !showPassword">👁</span>
        </div>
      </div>

      <div class="form-group">
        <label>Confirm Password</label>
        <div class="password-wrapper">
          <input
            :type="showConfirm ? 'text' : 'password'"
            placeholder="Confirm your Password"
            v-model="confirmPassword"
          />
          <span class="toggle" @click="showConfirm = !showConfirm">👁</span>
        </div>
      </div>

      <button class="register-btn" @click="register">
        Register
      </button>
    </div>
  </div>
</template>


<script setup>
import { ref } from "vue";

const firstName = ref("");
const lastName = ref("");
const securityCode = ref("");
const password = ref("");
const confirmPassword = ref("");

const showSecurity = ref(false);
const showPassword = ref(false);
const showConfirm = ref(false);

const register = () => {
  console.log({
    firstName: firstName.value,
    lastName: lastName.value,
    securityCode: securityCode.value,
    password: password.value,
    confirmPassword: confirmPassword.value,
  });
};
</script>

<style scoped>
.register-page {
  min-height: 100vh;
  background: url("../assets/bg2.jpg") no-repeat center center / cover;
  display: flex;
  justify-content: center;
  align-items: center;
}

.register-card {
  background: #e5e5e5;
  width: 480px;
  padding: 30px 25px;
  border-radius: 8px;
  text-align: center;
}

.logo {
  width: 70px;
  margin-bottom: 2px;
}

h2 {
  margin: 10px 0 5px;
  font-size: 29px;
  color: #000000;
}

.subtitle {
  font-size: 14px;
  color: #555;
  margin-bottom: 20px;
}

input {
  width: 100%;
  padding: 12px;
  margin-bottom: 15px;
  border-radius: 6px;
  border: 1px solid #bbb;
  background-color: #f4f7f8;
  color: #333;
  outline: none;
}

input::placeholder {
  color: #6c757d;
}

.password-wrapper {
  position: relative;
}

.toggle {
  position: absolute;
  right: 12px;
  top: 34%;
  transform: translateY(-50%);
  cursor: pointer;
  opacity: 0.7;
  color: #000000;
}

.toggle:hover {
  opacity: 1;
}

input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus {
  -webkit-box-shadow: 0 0 0 1000px #f4f7f8 inset !important;
  -webkit-text-fill-color: #333 !important;
}

.register-btn {
  width: 100%;
  padding: 14px;
  background: #cc3300;
  color: #fff;
  border: none;
  border-radius: 10px;
  font-weight: bold;
  cursor: pointer;
  margin-top: 10px;
}

.register-btn:hover {
  background: #b52d00;
}

.form-group {
  margin-bottom: 3px;
  text-align: left;
}

label {
  display: block;
  font-size: 13px;
  font-weight: 600;
  color: #000;
  margin-bottom: 6px;
}
</style>
