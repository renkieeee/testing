<template>
  <div class="page">
    <div class="card">
      <!-- Page Header -->
      <div class="page-header">
        <h2>History Records</h2>
        <button class="btn primary" @click="downloadCSV">Download CSV</button>
      </div>

      <!-- Search + Calendar Filter -->
      <div class="controls">
        <input
          type="text"
          placeholder="🔍 Search Tracking Number"
          v-model="search"
        />

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
        </div>

        <!-- Hidden calendar input -->
        <input ref="calendarInput" type="text" class="calendar-hidden-input" />
      </div>

      <!-- Table -->
      <table>
        <thead>
          <tr>
            <th>Tracking Number</th>
            <th>Date</th>
            <th>Action</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="record in paginatedRecords" :key="record.id">
            <td>{{ record.tracking }}</td>
            <td>{{ formatDate(record.date) }}</td>
            <td>{{ record.action }}</td>
          </tr>
        </tbody>
      </table>

      <!-- Pagination outside the table -->
      <div class="table-footer" v-if="totalPages > 1">
        <div class="pagination">
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
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import flatpickr from 'flatpickr'
import 'flatpickr/dist/flatpickr.css'

// Search input
const search = ref('')

// Pagination state
const currentPage = ref(1)
const pageSize = 5
const maxVisiblePages = 3

// Date range filter using flatpickr
const calendarInput = ref(null)
const dateRange = ref([])
let calendarInstance = null

onMounted(() => {
  calendarInstance = flatpickr(calendarInput.value, {
    mode: 'range',
    dateFormat: 'Y-m-d',
    clickOpens: false,
    onChange(selectedDates) {
      dateRange.value = [...selectedDates]
    },
  })
})

const openCalendar = () => calendarInstance?.open()
const clearDateFilter = () => {
  dateRange.value = []
  calendarInstance?.clear()
}

// Format date range text for display
const selectedRangeText = computed(() => {
  if (dateRange.value.length !== 2) return ''

  const [start, end] = dateRange.value
  const toISO = d => d.toISOString().slice(0, 10)

  return `${toISO(start)} – ${toISO(end)}`
})

// Sample history data - replace with your real data source
const records = ref([
  { id: 1, tracking: '2108227N92CTUY', date: '2025-01-12', action: 'Status changed to Outbound' },
  { id: 2, tracking: '5411903P77QZFX', date: '2025-01-12', action: 'Status changed to Pending' },
  { id: 3, tracking: '9043581G28JR', date: '2025-01-06', action: 'Platform changed to Lazada' },
  { id: 4, tracking: '1765004T59LBSP', date: '2025-01-03', action: 'Order added as Pending' },
  { id: 5, tracking: '3229660K14WHN', date: '2025-01-01', action: 'Status changed to Cancelled' },
  // Add more for pagination testing if needed
])

// Filtering logic
const filteredRecords = computed(() => {
  return records.value.filter(record => {
    // Search match
    const matchesSearch =
      !search.value || record.tracking.toLowerCase().includes(search.value.toLowerCase())

    // Date range match
    let matchesDate = true
    if (dateRange.value.length === 2) {
      const recordTime = new Date(record.date).setHours(0, 0, 0, 0)
      const startTime = new Date(dateRange.value[0]).setHours(0, 0, 0, 0)
      const endTime = new Date(dateRange.value[1]).setHours(23, 59, 59, 999)
      matchesDate = recordTime >= startTime && recordTime <= endTime
    }

    return matchesSearch && matchesDate
  })
})

// Pagination computations
const totalPages = computed(() =>
  Math.ceil(filteredRecords.value.length / pageSize)
)

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

const paginatedRecords = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  const end = start + pageSize
  return filteredRecords.value.slice(start, end)
})

// Format dates to readable string
const formatDate = isoDate => {
  if (!isoDate) return '-'
  return new Date(isoDate).toLocaleDateString(undefined, {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  })
}

// Dummy download CSV handler
const downloadCSV = () => {
  alert('Download CSV clicked — implement your logic here.')
}
</script>

<style scoped>
.page {
  padding: 24px;
  background: #f5f6f8;
  min-height: 100vh;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 20px;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  font-size: 25px;
  color: #000;
}

.controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
  gap: 12px;
  flex-wrap: wrap;
}

.controls input {
  width: 240px;
  padding: 8px 12px;
  border: 1px solid #000;
  border-radius: 4px;
  color: #000;
  background: white;
}

.controls input::placeholder {
  color: #999;
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
  border-radius: 6px;
  border: 1px solid #000;
}

.calendar-btn img {
  width: 20px;
  height: 20px;
}

.calendar-hidden-input {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.date-filter {
  display: flex;
  align-items: center;
  gap: 6px;
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
  color: #000;
}

.clear-btn {
  background: transparent;
  border: none;
  font-weight: bold;
  cursor: pointer;
  font-size: 14px;
  color: #c50c0c;
  padding: 0;
}

.clear-btn:hover {
  color: #ff4d4f;
}

/* Table styling */
table {
  width: 100%;
  border-collapse: collapse;
  font-size: 14px;
}

th,
td {
  padding: 12px 20px;
  border-bottom: 1px solid #ccc;
  text-align: center;
  color: #000;
}

th {
  font-weight: 600;
  color: #444;
}

.table-footer {
  margin-top: 16px;
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 12px;
}

.pagination {
  display: flex;
  gap: 6px;
}

.page-btn {
  padding: 6px 12px;
  border: 1px solid #000;
  background: #fff;
  cursor: pointer;
  border-radius: 4px;
  color: #000;
  transition: all 0.15s ease;
  user-select: none;
}

.page-btn.active {
  background: #888b8e;
  color: #fff;
}

.page-btn:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.page-info {
  font-size: 13px;
  color: #444;
}

.btn.primary {
  background-color: #777777;     /* Strong blue background */
  color: white;                  /* White text */
  padding: 10px 20px;
  border: none;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.25s ease;
  user-select: none;
  box-shadow: 0 3px 6px rgba(0, 82, 204, 0.4);
}

.btn.primary:hover {
  background-color: #003d99;    /* Darker blue on hover */
  box-shadow: 0 4px 10px rgba(0, 61, 153, 0.6);
}

.btn.primary:active {
  background-color: #002966;    /* Even darker on click */
  box-shadow: 0 2px 5px rgba(0, 41, 102, 0.7);
}

.btn.primary:disabled {
  background-color: #a0a8b9;
  cursor: not-allowed;
  box-shadow: none;
  color: #d0d4db;
}

</style>
