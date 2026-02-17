<template>
  <div class="page">
    <div class="card">
      <!-- Page Header -->
      <div class="page-header">
        <h2>Order Records</h2>
        <div>
          <button class="btn primary" @click="openAddParcelModal">+ Add Parcel</button>

          <button class="btn" :disabled="!selectedParcel" @click="openEdit">Edit</button>
        </div>
      </div>

      <!-- Search / Filter -->
      <div class="controls">
  <!-- Search input on the left -->
  <input
    type="text"
    placeholder="🔍 Search Tracking Number"
    v-model="search"
  />

  <!-- Right-side controls: date filter + calendar + filter button -->
  <div class="controls-right">
    <div class="date-filter">
      <div v-if="selectedRangeText" class="date-range-box">
  <span>{{ selectedRangeText }}</span>
  <button class="clear-btn" @click="clearDateFilter">X</button>
</div>

      <button class="btn calendar-btn" @click="openCalendar">
  <img src="../assets/calendar.png" alt="calendar" />
</button>
    </div>

    <button class="btn filter" @click="openFilter">Filter</button>

  </div>

  <!-- Hidden calendar input -->
  <input ref="calendarInput" type="text" class="calendar-hidden-input" />
</div>

      <!-- Table -->
      <table>
        <thead>
          <tr>
            <th></th>
            <th>Tracking Number</th>
            <th>Platform</th>
            <th>Date Added</th>
            <th>Outbound Date</th>
            <th>Status</th>
          </tr>
        </thead>

        <tbody>
          <tr
            v-for="parcel in paginatedParcels"
            :key="parcel.tracking"
            @click="toggleSelection(parcel)"
            :class="{ selected: selectedParcel === parcel }"
          >
            <td>
              <input type="radio" name="selectedParcel" :value="parcel" v-model="selectedParcel" />
            </td>
            <td>{{ parcel.tracking }}</td>
            <td>{{ parcel.platform }}</td>
            <td>{{ formatDate(parcel.dateAdded) }}</td>
            <td>{{ formatDate(parcel.outboundDate) }}</td>
            <td><span :class="['status', formatStatus(parcel.status)]">{{ parcel.status }}</span></td>
          </tr>
        </tbody>
              </table>

        <div class="table-footer">
       <div class="pagination" v-if="totalPages > 1">
  <div class="pagination-buttons">
    <button
      class="page-btn"
      :disabled="currentPage === 1"
      @click="currentPage = 1"
    >
      ‹‹
    </button>

    <button
      class="page-btn"
      :disabled="currentPage === 1"
      @click="currentPage--"
    >
      ‹
    </button>

    <button
      v-for="page in visiblePages"
      :key="page"
      class="page-btn"
      :class="{ active: currentPage === page }"
      @click="currentPage = page"
    >
      {{ page }}
    </button>

    <button
      class="page-btn"
      :disabled="currentPage === totalPages"
      @click="currentPage++"
    >
      ›
    </button>

    <button
      class="page-btn"
      :disabled="currentPage === totalPages"
      @click="currentPage = totalPages"
    >
      ››
    </button>
  </div>

  <span class="page-info">
    Page {{ currentPage }} of {{ totalPages }}
  </span>
</div>
</div>



    </div>

    <div
  v-if="showAddParcelModal"
  class="modal-backdrop"
  @click.self="closeAddParcelModal"
>
  <div class="modal">
    <button class="modal-close" @click="closeAddParcelModal">×</button>
    <h3>Add Parcel</h3>

    <div class="form-group">
      <label>Tracking Number</label>
      <input
  v-model="addForm.tracking"
  placeholder="Enter tracking number"
  type="text"
  :disabled="!addForm.manualInput"
/>
    </div>

    <div class="form-group">
      <label>
        <input type="checkbox" v-model="addForm.manualInput" />
        Manual Input
      </label>
    </div>

    <div class="form-group">
      <label>Platform</label>
      <select v-model="addForm.platform">
        <option disabled value="">Select platform</option>
        <option>J&T</option>
        <option>Shopee</option>
        <option>NinjaVan</option>
        <option>Lazada</option>
      </select>
    </div>

    <div class="form-group">
      <label>Status</label>
      <select v-model="addForm.status">
        <option disabled value="">Select status</option>
        <option>Pending</option>
        <option>Returned</option>
        <option>Cancelled</option>
        <option>Double Waybill</option>
        <option>Outbound</option>
      </select>
    </div>

    <div class="form-actions">
      <button class="btn primary" @click="submitAddParcel">Add Parcel</button>
    </div>
  </div>
</div>

    <!-- EDIT POPUP -->
    <div v-if="showEditModal" class="modal-backdrop">
      <div class="modal">
        <button class="modal-close" @click="showEditModal = false">×</button>
        <h3>Edit Order</h3>

        <div class="form-group">
          <label>Tracking Number</label>
          <input v-model="editForm.tracking" disabled />
        </div>

        <div class="form-group">
          <label>Edit Platform</label>
          <select v-model="editForm.platform">
            <option>J&T</option>
            <option>Shopee</option>
            <option>NinjaVan</option>
            <option>Lazada</option>
          </select>
        </div>

        <div class="form-group">
          <label>Edit Status</label>
          <select v-model="editForm.status">
            <option>Pending</option>
            <option>Returned</option>
            <option>Cancelled</option>
            <option>Outbound</option>
            <option>Double Waybill</option>
          </select>
        </div>

        <div class="form-actions">
          <button class="btn primary" @click="updateParcel">Update</button>
        </div>
      </div>
    </div>
  </div>

  <!-- FILTER POPUP -->
<div v-if="showFilterPopup" class="filter-backdrop" @click.self="closeFilter">
  <div class="filter-popup">
    <h4>Status</h4>
    <div class="filter-group">
      <label v-for="(v, key) in filterStatus" :key="key">
        <input type="checkbox" v-model="filterStatus[key]" />
        {{ key }}
      </label>
    </div>

    <h4>Platform</h4>
    <div class="filter-group">
      <label v-for="(v, key) in filterPlatform" :key="key">
        <input type="checkbox" v-model="filterPlatform[key]" />
        {{ key }}
      </label>
    </div>

    <div class="filter-actions">
      <button class="btn" @click="clearFilter">Clear</button>
      <button class="btn primary" @click="closeFilter">Filter</button>
    </div>
  </div>
</div>

<div class="card-buttons">
    <button class="btn primary">Download CSV</button>
    <button class="btn">Report Issues</button>
</div>
</template>


<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import flatpickr from 'flatpickr'
import 'flatpickr/dist/flatpickr.css'

const search = ref('')
const selectedParcel = ref(null)
const showEditModal = ref(false)

const currentPage = ref(1)
const pageSize = 15
const maxVisiblePages = 3

const visiblePages = computed(() => {
  const pages = []
  let start = currentPage.value - 1
  let end = currentPage.value + 1

  if (start < 1) {
    start = 1
    end = Math.min(maxVisiblePages, totalPages.value)
  }

  if (end > totalPages.value) {
    end = totalPages.value
    start = Math.max(1, end - maxVisiblePages + 1)
  }

  for (let i = start; i <= end; i++) {
    pages.push(i)
  }

  return pages
})


const calendarInput = ref(null)
let calendarInstance = null
const dateRange = ref([])

onMounted(() => {
  calendarInstance = flatpickr(calendarInput.value, {
    mode: 'range',
    dateFormat: 'Y-m-d',
    clickOpens: false,
    onChange(selectedDates) {
      dateRange.value = [...selectedDates] // reactive update
    },
  })
})

const openCalendar = () => calendarInstance?.open()
const clearDateFilter = () => {
  dateRange.value = []
  calendarInstance?.clear()
}

const selectedRangeText = computed(() => {
  if (dateRange.value.length !== 2) return ''

  const [start, end] = dateRange.value

  const toISO = d => d.toISOString().slice(0, 10)

  return `${toISO(start)} – ${toISO(end)}`
})

const parcels = ref([
])

const paginatedParcels = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  const end = start + pageSize
  return filteredParcels.value.slice(start, end)
})

const totalPages = computed(() =>
  Math.ceil(filteredParcels.value.length / pageSize)
)

const filteredParcels = computed(() => {
  return parcels.value.filter(parcel => {
    // 🔍 Search
    const matchesSearch =
      !search.value || parcel.tracking.includes(search.value)

    // 📅 Date range
    let matchesDate = true
    if (dateRange.value.length === 2) {
      const parcelTime = new Date(parcel.dateAdded).setHours(0,0,0,0)
      const startTime = new Date(dateRange.value[0]).setHours(0,0,0,0)
      const endTime = new Date(dateRange.value[1]).setHours(23,59,59,999)
      matchesDate = parcelTime >= startTime && parcelTime <= endTime
    }

    // 📦 Status filter
    const activeStatuses = Object.keys(filterStatus.value)
      .filter(k => filterStatus.value[k])

    const matchesStatus =
      activeStatuses.length === 0 || activeStatuses.includes(parcel.status)

    // 🏷 Platform filter
    const activePlatforms = Object.keys(filterPlatform.value)
      .filter(k => filterPlatform.value[k])

    const matchesPlatform =
      activePlatforms.length === 0 || activePlatforms.includes(parcel.platform)

    return matchesSearch && matchesDate && matchesStatus && matchesPlatform
  })
})


const formatStatus = status => status.toLowerCase().replace(/\s+/g, '-')

const addParcel = () => {
  parcels.value.unshift({
    tracking: Date.now().toString(),
    platform: 'Shopee',
    dateAdded: new Date().toISOString().slice(0, 10),
    outboundDate: null,
    status: 'Pending'
  })
}

const formatDate = iso => iso ?? '-'


const editForm = ref({ tracking: '', platform: '', status: '' })
const openEdit = () => { editForm.value = { ...selectedParcel.value }; showEditModal.value = true }
const updateParcel = () => { Object.assign(selectedParcel.value, editForm.value); showEditModal.value = false }
const toggleSelection = parcel => selectedParcel.value = selectedParcel.value === parcel ? null : parcel

const showAddParcelModal = ref(false);

const addForm = ref({
  tracking: "",
  manualInput: false,
  platform: "",
  status: "",
});

const openAddParcelModal = () => {
  addForm.value = {
    tracking: "",
    manualInput: false,
    platform: "",
    status: "",
  };
  showAddParcelModal.value = true;
};

const closeAddParcelModal = () => {
  showAddParcelModal.value = false;
};

const submitAddParcel = () => {
  if (
    !addForm.value.tracking.trim() ||
    !addForm.value.platform ||
    !addForm.value.status
  ) {
    alert("Please fill all required fields.");
    return;
  }

  parcels.value.unshift({
    tracking: addForm.value.tracking.trim(),
    platform: addForm.value.platform,
    dateAdded: new Date().toISOString().slice(0, 10),
    outboundDate: null,
    status: addForm.value.status,
  });

  closeAddParcelModal();
};

const showFilterPopup = ref(false)

const filterStatus = ref({
  Returned: false,
  Cancelled: false,
  Pending: false,
  'Double Waybill': false,
  Outbound: false,
})

const filterPlatform = ref({
  'J&T': false,
  Shopee: false,
  NinjaVan: false,
  Lazada: false,
})

const openFilter = () => {
  showFilterPopup.value = true
}

const closeFilter = () => {
  showFilterPopup.value = false
}

const clearFilter = () => {
  Object.keys(filterStatus.value).forEach(k => filterStatus.value[k] = false)
  Object.keys(filterPlatform.value).forEach(k => filterPlatform.value[k] = false)
}

watch(
  [search, dateRange, filterStatus, filterPlatform],
  () => {
    currentPage.value = 1
  },
  { deep: true }
)

</script>



<style scoped>
/* Page spacing (after sidebar) */
.page {
  padding: 24px;
  background: #f5f6f8;
  min-height: 100vh;
}

/* White content card */
.card {
  background: #ffffff;
  border-radius: 10px;
  padding: 20px;
}

/* Header inside page */
.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  color: #000000;
  font-size: 25px;
}

/* Controls */
.controls {
  display: flex;
  align-items: center;
  justify-content: space-between; /* left = search, right = other controls */
  flex-wrap: wrap; /* nice on small screens */
  gap: 12px;
}
.controls-right {
  display: flex;
  align-items: center;
  gap: 8px;
}
.calendar-btn {
  padding: 6px 12px;
  font-size: 18px;
  border: none;
  background: #f5f5f5;
  cursor: pointer;
  flex-shrink: 0;
}

.calendar-popup {
  position: absolute;
  top: 38px; /* adjust based on button height */
  left: 90px; /* adjust or calculate for your layout */
  background: white;
  border: 1px solid #ccc;
  padding: 12px 16px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0,0,0,0.1);
  z-index: 10000;
  display: flex;
  flex-direction: column;
  gap: 8px;
  min-width: 230px;
}

.calendar-popup label {
  font-size: 12px;
  display: flex;
  flex-direction: column;
}

.calendar-popup input[type="date"] {
  padding: 6px;
  margin-top: 4px;
  border: 1px solid #000;
  border-radius: 4px;
}

.calendar-actions {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}

.calendar-actions .btn {
  padding: 6px 12px;
  font-size: 13px;
  border-radius: 5px;
  border: 1px solid #000;
  cursor: pointer;
  background: #f0f0f0;
}

.controls input {
  background: #ffffff;
  color: #000000;
  padding: 8px 12px;
  border-radius: 4px;
  border: 1px solid #000;
  width: 240px;
}

.controls input::placeholder {
  color: #000000;
}

/* Table */
table {
  width: 100%;
  border-collapse: collapse;
  table-layout: fixed; /* ⭐ CRITICAL */
  font-size: 14px;
}

th,
td {
  padding: 11px 45px;
  border-bottom: 1px solid #eaeaea;
  color: #000000;
}
th:nth-child(2), td:nth-child(2) { width: 23%; } /* Tracking Number */
th:nth-child(3), td:nth-child(3) { width: 15%; } /* Platform */
th:nth-child(4), td:nth-child(4) { width: 24%; } /* Date Added */
th:nth-child(5), td:nth-child(5) { width: 24%; } /* Outbound Date */
th:nth-child(6), td:nth-child(6) { width: 30%; } /* Status */

th {
  font-weight: 600;
  color: #444;
}

/* Status Pills */
.status {
  padding: 6px 14px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 500;
  color: #000000;
}

.status.pending {
  background: #ffd54f;
  color: #333;
}
.status.returned {
  background: #b3c7ff;
}
.status.outbound {
  background: #d7a6ff;
}
.status.cancelled {
  background: #ffa6a6;
}
.status.double-waybill {
  background: #9be59b;
}

/* Buttons */
.btn {
  padding: 8px 14px;
  border-radius: 6px;
  border: 1px solid #000;
  cursor: pointer;
  background: #ffffff;
  color: #000000;
  font-size: 15px;
}

.btn.primary {
  background: #888b8e;
  color: #fff;
  margin-right: 12px;
  font-size: 15px;
}

.btn.filter {
  background: #ffffff;
  color: #000000;
  top: 187px; /* distance from top of screen */
  right: 89px;
  border: 1px solid #000;
  position: static;
  font-size: 15px;
}
.modal {
  position: fixed;
  top: 50%;
  left: 55%;
  transform: translate(-50%, -50%);
  background: #ffffff;
  padding: 40px;
  border: 1px solid #000;
  width: 360px; /* portrait width */
  z-index: 1000;
  color: #000000;
  font-size: 22px;
}

.modal h3 {
  text-align: center;
  margin-bottom: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 12px;
}

.form-group label {
  font-size: 12px;
  margin-bottom: 4px;
}

.form-group input,
.form-group select {
  padding: 6px;
  color: #000000;
  background: #e7e7e7;
}

.form-actions {
  display: flex;
  justify-content: center;
  margin-top: 22px;
  color: #1f1f1f;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none; /* no hover, no click */
}
.modal-close {
  position: absolute;
  top: 10px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 22px;
  cursor: pointer;
  color: #000;
  line-height: 1;
}

.modal-close:hover {
  opacity: 0.6;
}

.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.3); /* dim */
  backdrop-filter: blur(6px); /* blur */
  -webkit-backdrop-filter: blur(6px);
  z-index: 999;

  display: flex;
  align-items: center;
  justify-content: center;

  pointer-events: all; /* blocks clicks behind */
}

tr.selected td {
  background-color: #e6e6e6; /* grey behind text */
}

.calendar-hidden-input {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.flatpickr-calendar {
  background-color: #ffffff; /* main background */
  border: 1px solid #000000; /* border color */
  color: #000000; /* text color */
  z-index: 10001 !important;
}
.flatpickr-months {
  background-color: #f0f0f0;
  color: #333333;
}
.flatpickr-month {
  font-weight: bold;
}

/* Weekday names */
.flatpickr-weekdays {
  background-color: #f7f7f7;
  color: #555555;
}

.date-filter {
  display: flex;
  align-items: center;
  gap: 6px;
}

.date-range-text {
  font-size: 13px;
  padding: 6px 10px;
  border: 1px solid #000;
  border-radius: 6px;
  background: #e3e2e2;
  color: #000;
}

.clear-date {
  padding: 6px 12px;
  font-size: 13px;
  background: #e3e2e2;
  color: #000000;
}

.btn.calendar-btn {
  border: 1px solid #000000;
  border-radius: 6px;
  background-color: #ffffff;
  color: #ffffff;
  padding: 6px 12px;
  font-size: 18px;
  cursor: pointer;
  transition: all 0.2s ease;
}
.btn.filter {
  padding: 8px 14px;
  border-radius: 6px;
  border: 1px solid #000;
  background: #ffffff;
  cursor: pointer;
}

.date-range-box {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 6px 10px;
  border: 1px solid #000;
  border-radius: 6px;
  background: #f1f1f1;
  font-size: 13px;
  color: #000000;
}

.date-range-box span {
  white-space: nowrap;
}

.clear-btn {
  background: transparent;
  border: none;
  font-weight: bold;
  cursor: pointer;
  font-size: 14px;
  line-height: 1;
  color: #c50c0c;
  padding: 0;
}
.clear-btn:hover {
  color: #ff4d4f;
}
.calendar-btn img {
  width: 20px;    /* adjust size as needed */
  height: 20px;
  display: block;
}

.modal-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(6px);
  -webkit-backdrop-filter: blur(6px);
  z-index: 9999;

  display: flex;
  align-items: center;
  justify-content: center;

  pointer-events: all;
}

.modal {
  position: fixed;
  top: 50%;
  left: 55%;
  transform: translate(-50%, -50%);
  background: #ffffff;
  padding: 40px;
  border: 1px solid #000;
  width: 360px;
  z-index: 10000;
  color: #000000;
  font-size: 22px;
  border-radius: 8px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.2);
}

.modal h3 {
  text-align: center;
  margin-bottom: 15px;
}

.form-group {
  display: flex;
  flex-direction: column;
  margin-bottom: 12px;
}

.form-group label {
  font-size: 12px;
  margin-bottom: 4px;
}

.form-group input,
.form-group select {
  padding: 8px;
  color: #000000;
  background: #e7e7e7;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 16px;
}

.form-actions {
  display: flex;
  justify-content: center;
  margin-top: 22px;
  color: #1f1f1f;
}

.modal-close {
  position: absolute;
  top: 10px;
  right: 12px;
  background: transparent;
  border: none;
  font-size: 26px;
  cursor: pointer;
  color: #000;
  line-height: 1;
}

.modal-close:hover {
  opacity: 0.6;
}

.filter-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.2);
  z-index: 9999;
}

.filter-popup {
  position: absolute;
  top: 240px;
  right: 80px;
  background: #fff;
  border: 1px solid #000;
  padding: 16px;
  width: 260px;
  border-radius: 8px;
  color: #000000;
}

.filter-popup h4 {
  margin: 8px 0;
  font-size: 14px;
}

.filter-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 6px;
  font-size: 13px;
}

.filter-group label {
  display: flex;
  gap: 6px;
  align-items: center;
  color: #000000;
}

.filter-actions {
  display: flex;
  justify-content: space-between;
  margin-top: 14px;
}

.bottom-right-buttons {
  position: static;  /* now relative to .page, not viewport */
  bottom: 20px;
  right: 20px;
  display: flex;
  gap: 12px;
  z-index: 1000;
}
.card-buttons {
  display: flex;
  justify-content: flex-end; /* push buttons to the right */
  margin-top: 1px;          /* space below the card */
  gap: 12px;    
  margin-right: 30px;             
}
td:first-child, th:first-child {
  width: 40px;       /* optional: make it narrow */
  padding-left: 8px; /* less space from left edge */
  text-align: center; /* centers the radio vertically */
}
input[type="radio"] {
  margin: 0;      /* remove default margins */
  transform: scale(1); /* adjust size if needed */
}
/* Center all table rows data */
table td {
  text-align: center;    /* centers horizontally */
  vertical-align: middle; /* centers vertically */
}

.pagination {
  position: relative;
  left: 50%;
  transform: translateX(-50%);
  width: max-content;
  margin-top: 16px;
}


.pagination-buttons {
  display: flex;
  gap: 6px;
}


.page-btn {
  padding: 6px 12px;
  border: 1px solid #000;
  background: #fff;
  cursor: pointer;
  border-radius: 4px;
  color: #000000;
  transition: all 0.15s ease;
}

.page-btn.active {
  background: #888b8e;
  color: #fff;
}

.page-btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
  color: #000000;
}

.page-info {
  font-size: 13px;
  color: #444;
  margin: 0 8px;
  white-space: nowrap;
    position: relative;
  left: 30%;
}

.table-footer {
  padding: 0 45px;           /* ⭐ SAME as td padding */
}
</style>
