<template>
  <div class="page">
    <h1 class="page-title">User Management</h1>

    <div class="grid">
      <!-- Profile Card -->
      <div class="card profile-card">
        <div class="profile-circle">
          <span class="profile-icon">👤</span>
        </div>
        <div class="profile-text">
          <h2>Hi, [Name]!</h2>
          <p class="role">Admin</p>
          <p class="started">Started on [Date]</p>
        </div>
      </div>

      <!-- Active Users Card -->
      <div class="card stat-card">
        <p class="stat-label">Active Users</p>
        <p class="stat-value">0</p>
      </div>

      <!-- Inactive Users Card -->
      <div class="card stat-card">
        <p class="stat-label">Inactive Users</p>
        <p class="stat-value">0</p>
      </div>

      <!-- Security Code Card -->
      <div class="card security-card">
        <h3>Security Code</h3>
        <input class="security-input" type="password" value="**********" readonly />
        <div class="show-code">
          <input type="checkbox" id="show" />
          <label for="show">Show Security Code</label>
        </div>
      </div>

      <!-- Users Table Card -->
      <div class="card users-card">
        <div class="users-header">
          <h3>Users</h3>
          <button class="edit-btn">EDIT</button>
        </div>

        <table class="users-table">
          <thead>
            <tr>
              <th></th>
              <th>Employee Name</th>
              <th>Role</th>
              <th>Status</th>
              <th>Reason</th>
            </tr>
          </thead>

          <tbody>
            <tr v-for="n in 6" :key="n">
              <td><input type="checkbox" /></td>
              <td>—</td>
              <td>—</td>
              <td class="status">—</td>
              <td>—</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup>
</script>

<style scoped>
.page {
  padding: 32px;
  background: #fff;
  min-height: 100vh;
}

.page-title {
  font-size: 32px;
  margin-bottom: 24px;
  color: #000;
}

/* Grid Layout */
.grid {
  display: grid;
  grid-template-columns: 340px 230px 230px;
  grid-template-rows: auto auto;
  gap: 24px;
}

/* Common card styles */
.card {
  background: #fff;
  border-radius: 10px;
  padding: 18px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  color: #000;
}

/* Profile Card */
.profile-card {
  grid-column: 1 / 2;
  display: flex;
  gap: 18px;
  align-items: center;
}

.profile-circle {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  background: #2f2f2f;
  display: flex;
  align-items: center;
  justify-content: center;
}

.profile-icon {
  color: #fff;
  font-size: 34px;
}

.profile-text h2 {
  margin: 0;
  font-size: 20px;
}

.profile-text .role {
  margin: 4px 0;
  font-style: italic;
  color: #666;
}

.profile-text .started {
  margin: 6px 0;
  color: #7c7c7c;
}

/* Stat Cards */
.stat-card {
  padding: 18px;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.stat-label {
  margin: 0;
  font-size: 14px;
  color: #666;
}

.stat-value {
  font-size: 32px;
  margin: 10px 0 0;
}

/* Security Code Card */
.security-card {
  grid-column: 1 / 2;
}

.security-card h3 {
  margin: 0 0 12px;
  font-size: 16px;
}

.security-input {
  width: 100%;
  padding: 8px;
  border: 1px solid #000;
  border-radius: 3px;
  background: #6d6d6d;
}

.show-code {
  margin-top: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
  font-size: 14px;
}

/* Users Table Card */
.users-card {
  grid-column: 2 / 4;
}

.users-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.edit-btn {
  padding: 6px 10px;
  border: 1px solid #000;
  background: #fff;
  cursor: pointer;
  font-size: 12px;
  color: #000;
}

.users-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 12px;
}

.users-table th,
.users-table td {
  border-bottom: 1px solid #e0e0e0;
  padding: 12px;
  text-align: left;
}

.users-table th {
  color: #888;
}

.status {
  color: #888;
}
</style>
