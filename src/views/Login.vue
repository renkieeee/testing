<template>
  <div class="login-page">
    <div class="login-card">
      <img src="../assets/logo1.png" alt="Logo" class="logo" />

      <h2>Login</h2>
      <p class="subtitle">View your growth and connect!</p>

      <input
        type="text"
        placeholder="Enter your user id"
        v-model="userId"
      />

      <div class="password-wrapper">
        <input
          :type="showPassword ? 'text' : 'password'"
          placeholder="Enter your password"
          v-model="password"
        />
        <span class="toggle" @click="showPassword = !showPassword">
          👁
        </span>
      </div>

      <router-link to="/forgot-password" class="forgot">
  Forgot password?
</router-link>

      <router-link to="/dashboard" class="login-btn">
  Login
</router-link>

      <div class="divider">
        <span></span>
        <p>Or</p>
        <span></span>
      </div>

      <router-link to="/register" class="register-btn">
  Register
</router-link>

    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const userId = ref("");
const password = ref("");
const showPassword = ref(false);

const login = () => {
  console.log("User ID:", userId.value);
  console.log("Password:", password.value);
};
</script>

<style scoped>
.login-page {
  min-height: 100vh;
  background: url("../assets/bg2.jpg") no-repeat center center / cover;
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-card {
  background: #e5e5e5;
  width: 360px;
  padding: 30px 25px;
  border-radius: 8px;
  text-align: center;
}

.logo {
  width: 70px;
  margin-bottom: 2px;
}

h2 {
  margin: 5px 0 9px;
  font-size: 34px;
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
  border: 1px solid #ccc;
  color: #000000;  
  outline: none;
  background-color: #ffffff;
}

input::placeholder {
  color: #000000;            
}

input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus {
  -webkit-box-shadow: 0 0 0 1000px #5f5f5f inset !important;
  -webkit-text-fill-color: #ffffff !important;
}

.password-wrapper {
  position: relative;
}

.toggle {
  position: absolute;
  right: 12px;
  top: 36%;
  transform: translateY(-50%);
  cursor: pointer;
  color: #000000;  
}

.forgot {
  font-size: 13px;
  color: #0334a5;
  text-decoration: underline;
  display: block;
  margin-bottom: 20px;
}

.login-btn {
  width: 100%;
  padding: 11px;
  background: #cc3300;
  border: 2px solid #cc3300;
  color: #ffffff;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  display: block;  
  text-align: center; 
  text-decoration: none; 

}

.divider {
  display: flex;
  align-items: center;
  margin: 20px 0;
}

.divider span {
  flex: 1;
  height: 1px;
  background: #aaa;
}

.divider p {
  margin: 0 10px;
  font-size: 14px;
  color: #000000;
}

.register-btn {
  width: 100%;
  padding: 11px;
  background: transparent;
  border: 2px solid #cc3300;
  color: #cc3300;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;

  display: block;
  text-align: center;
  text-decoration: none;
}
</style>
