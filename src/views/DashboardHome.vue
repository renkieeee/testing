<template>
  <div>
    <div class="topbar">
      <h1>Monthly Report</h1>
      <div class="topbar-right">
        <span class="date">May, 2025</span>
        <button class="icon-btn">◀</button>
        <button class="icon-btn">▶</button>
        <button class="icon-btn">📅</button>
      </div>
    </div>

    <div class="stats">
      <div class="stat-card"><p>Returned</p><h2>—</h2></div>
      <div class="stat-card"><p>Pending</p><h2>—</h2></div>
      <div class="stat-card"><p>Cancelled</p><h2>—</h2></div>
      <div class="stat-card"><p>Double Waybill</p><h2>—</h2></div>
      <div class="stat-card"><p>Outbound</p><h2>—</h2></div>
    </div>

    <section class="card">
      <h3>Unresolved Reports</h3>
      <div class="list-row"><span class="error">✖</span><span>Order ID XXXXXXXX Still Unresolved</span><span class="date-right">—</span></div>
      <div class="list-row"><span class="error">✖</span><span>Order ID XXXXXXXX Still Unresolved</span><span class="date-right">—</span></div>
      <div class="list-row"><span class="error">✖</span><span>Order ID XXXXXXXX Still Unresolved</span><span class="date-right">—</span></div>
      <a class="link">Go to Report/Issue Tab</a>
    </section>

    <div class="bottom">
      <section class="card">
        <h3>History Records</h3>
        <div class="history-row success">✔ Tracking Number XXXXX edited from Pending to Outbound</div>
        <div class="history-row">➕ New Parcel Registered: XXXXX</div>
        <div class="history-row warning">⚠ Tracking Number XXXXX edited from Pending to Cancelled</div>
        <a class="link">View Full History Records Tab</a>
      </section>

      <section class="card chart">
        <h3>Parcel Status Chart</h3>
        <div class="chart-placeholder">Chart goes here</div>
      </section>
    </div>
  </div>
</template>

<style scoped>
.topbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  color: #000000;
  font-size: 14px;
}

.topbar-right {
  display: flex;
  align-items: center;
  gap: 10px;
  color: #000000;
}

.icon-btn {
  border: none;
  background: #fff;
  padding: 6px 10px;
  cursor: pointer;
  border-radius: 4px;
}

.user {
  background: #fff;
  padding: 6px 12px;
  border-radius: 20px;
  font-size: 14px;
}

.stats {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 15px;
  margin-bottom: 20px;
}

.stat-card {
  background: #fff;
  border-radius: 8px;
  padding: 15px;
  box-shadow: 0 1px 4px rgba(0,0,0,.1);
}

.stat-card p { font-size: 14px; color: #777; }
.stat-card h2 { margin-top: 8px; font-size: 28px; }

.card {
  background: #fff;
  border-radius: 8px;
  padding: 20px;
  margin-bottom: 20px;
  box-shadow: 0 1px 4px rgba(0,0,0,.1);
  color: #000000;
}

.card h3 { margin-bottom: 15px; }

.list-row {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 0;
  border-bottom: 1px solid #eee;
}

.date-right { margin-left: auto; color: #777; font-size: 13px; }
.error { color: red; }

.history-row { padding: 10px 0; border-bottom: 1px solid #eee; }
.success { color: green; }
.warning { color: #d9534f; }

.link { display: inline-block; margin-top: 12px; color: #1a73e8; cursor: pointer; font-size: 14px; }

.bottom { display: grid; grid-template-columns: 2fr 1fr; gap: 20px; }

.chart-placeholder {
  height: 220px;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  border: 2px dashed #ddd;
  border-radius: 8px;
}
</style>
