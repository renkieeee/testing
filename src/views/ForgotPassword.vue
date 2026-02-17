<template>
  <div class="forgot-page">
    <div class="forgot-card">
      <img src="../assets/logo1.png" alt="Logo" class="logo" />

      <h2>Reset Password</h2>
      <p class="subtitle">Enter your details to reset your password</p>

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
        <label>New Password</label>
        <div class="password-wrapper">
          <input
            :type="showPassword ? 'text' : 'password'"
            placeholder="Enter your new password"
            v-model="newPassword"
          />
          <span class="toggle" @click="showPassword = !showPassword">👁</span>
        </div>
      </div>

      <div class="form-group">
        <label>Confirm New Password</label>
        <div class="password-wrapper">
          <input
            :type="showConfirm ? 'text' : 'password'"
            placeholder="Re-enter your new password"
            v-model="confirmPassword"
          />
          <span class="toggle" @click="showConfirm = !showConfirm">👁</span>
        </div>
      </div>

      <button class="reset-btn" @click="resetPassword">
        Reset Password
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const securityCode = ref("");
const newPassword = ref("");
const confirmPassword = ref("");

const showSecurity = ref(false);
const showPassword = ref(false);
const showConfirm = ref(false);

const resetPassword = () => {
  console.log({
    securityCode: securityCode.value,
    newPassword: newPassword.value,
    confirmPassword: confirmPassword.value,
  });
};
</script>

<style scoped>
.forgot-page {
  min-height: 100vh;
  background: url("../assets/bg2.jpg") no-repeat center center / cover;
  display: flex;
  justify-content: center;
  align-items: center;
}

.forgot-card {
  background: #e5e5e5;
  width: 380px;
  padding: 30px 25px;
  border-radius: 8px;
  text-align: center;
}

.logo {
  width: 70px;
  margin-bottom: 10px;
}

h2 {
  margin: 10px 0 5px;
  font-size: 29px;
  color: #000;
}

.subtitle {
  font-size: 14px;
  color: #555;
  margin-bottom: 20px;
}

/* ✅ NEW */
.form-group {
  margin-bottom: 16px;
  text-align: left;
}

label {
  display: block;
  font-size: 13px;
  font-weight: 600;
  color: #000;
  margin-bottom: 6px;
}

input {
  width: 100%;
  padding: 12px;
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

/* ✅ FIXED CENTERING */
.toggle {
  position: absolute;
  right: 12px;
  top: 50%;
  transform: translateY(-50%);
  cursor: pointer;
  opacity: 0.7;
  color: #000;
}

.toggle:hover {
  opacity: 1;
}

/* Autofill fix */
input:-webkit-autofill,
input:-webkit-autofill:hover,
input:-webkit-autofill:focus {
  -webkit-box-shadow: 0 0 0 1000px #f4f7f8 inset !important;
  -webkit-text-fill-color: #333 !important;
}

.reset-btn {
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

.reset-btn:hover {
  background: #b52d00;
}
</style>
