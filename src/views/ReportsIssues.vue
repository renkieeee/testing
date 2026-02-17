<template>
  <div class="page">
    <div class="card">
      <!-- Header -->
      <div class="page-header">
        <h2>Reports / Issues</h2>
        <button class="btn" :disabled="!selectedIssue" @click="openEdit">
          Edit
        </button>
      </div>

      <!-- Controls -->
      <div class="controls">
        <input
          type="text"
          placeholder="🔍 Search Tracking Number"
          v-model="search"
        />

        <button class="btn filter" @click="openFilter">Filter</button>
      </div>

      <!-- Table -->
      <table>
        <thead>
          <tr>
            <th></th>
            <th>Tracking Number</th>
            <th>Date Reported</th>
            <th>Remarks</th>
            <th>Status</th>
          </tr>
        </thead>

        <tbody>
          <tr
            v-for="issue in paginatedIssues"
            :key="issue.tracking"
            @click="toggleSelection(issue)"
            :class="{ selected: selectedIssue === issue }"
          >
            <td>
              <input
                type="radio"
                name="selectedIssue"
                :value="issue"
                v-model="selectedIssue"
              />
            </td>
            <td>{{ issue.tracking }}</td>
            <td>{{ issue.dateReported }}</td>
            <td class="remarks">{{ issue.remarks }}</td>
            <td>
              <span :class="['status', issue.status.toLowerCase()]">
                {{ issue.status }}
              </span>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Pagination -->
      <div class="table-footer">
        <div class="pagination" v-if="totalPages > 1">
          <div class="pagination-buttons">
            <button class="page-btn" :disabled="currentPage === 1" @click="currentPage = 1">
              ‹‹
            </button>

            <button class="page-btn" :disabled="currentPage === 1" @click="currentPage--">
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

            <button class="page-btn" :disabled="currentPage === totalPages" @click="currentPage++">
              ›
            </button>

            <button class="page-btn" :disabled="currentPage === totalPages" @click="currentPage = totalPages">
              ››
            </button>
          </div>

          <span class="page-info">
            Page {{ currentPage }} of {{ totalPages }}
          </span>
        </div>
      </div>
    </div>

    <!-- EDIT MODAL -->
    <div v-if="showEditModal" class="modal-backdrop">
      <div class="modal edit-modal">
        <button class="modal-close" @click="closeEdit">×</button>

        <h3>Edit Reports / Issues</h3>

        <div class="form-group">
          <label>Tracking Number</label>
          <div class="readonly-box">{{ editForm.tracking }}</div>
        </div>

        <div class="form-group">
          <label>Remarks</label>
          <textarea
            v-model="editForm.remarks"
            placeholder="Enter Text"
            class="remarks-textbox"
          ></textarea>
        </div>

        <div class="form-group">
          <label>Edit Status</label>

          <div class="status-options">
            <label>
              <input
                type="radio"
                value="Unresolved"
                v-model="editForm.status"
              />
              Unresolved
            </label>

            <label>
              <input
                type="radio"
                value="Resolved"
                v-model="editForm.status"
              />
              Resolved
            </label>
          </div>
        </div>

        <div class="form-actions">
          <button class="btn primary update-btn" @click="updateIssue">
            Update
          </button>
        </div>
      </div>
    </div>

    <!-- FILTER POPUP -->
    <div v-if="showFilterPopup" class="filter-backdrop">
      <div class="filter-popup">
        <h4>Status</h4>

        <div class="filter-group">
          <label>
            <input type="checkbox" v-model="filterStatus.Resolved" />
            Resolved
          </label>
          <label>
            <input type="checkbox" v-model="filterStatus.Unresolved" />
            Unresolved
          </label>
        </div>

        <div class="filter-actions">
          <button class="btn" @click="clearFilter">Clear</button>
          <button class="btn primary" @click="closeFilter">Apply</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'

const search = ref('')
const selectedIssue = ref(null)
const showEditModal = ref(false)
const showFilterPopup = ref(false)

const issues = ref([
  {
    tracking: '222HDRJ0A9S',
    dateReported: 'April 12, 2025',
    remarks: 'Lorem ipsum dolor sit amet, consectetur...',
    status: 'Unresolved',
  },
  {
    tracking: '992DDHDKL9',
    dateReported: 'April 17, 2025',
    remarks: 'Lorem ipsum dolor sit amet, consectetur...',
    status: 'Resolved',
  },
  {
    tracking: '75LZXC993X',
    dateReported: 'May 28, 2025',
    remarks: 'Lorem ipsum dolor sit amet, consectetur...',
    status: 'Unresolved',
  },
])

const filterStatus = ref({
  Resolved: false,
  Unresolved: false,
})

const filteredIssues = computed(() => {
  return issues.value.filter(issue => {
    const matchesSearch =
      !search.value || issue.tracking.includes(search.value)

    const activeStatuses = Object.keys(filterStatus.value)
      .filter(k => filterStatus.value[k])

    const matchesStatus =
      activeStatuses.length === 0 || activeStatuses.includes(issue.status)

    return matchesSearch && matchesStatus
  })
})

// Pagination
const currentPage = ref(1)
const pageSize = 15
const maxVisiblePages = 3

const totalPages = computed(() =>
  Math.ceil(filteredIssues.value.length / pageSize)
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

const paginatedIssues = computed(() => {
  const start = (currentPage.value - 1) * pageSize
  const end = start + pageSize
  return filteredIssues.value.slice(start, end)
})

watch([search, filterStatus], () => {
  currentPage.value = 1
}, { deep: true })

const toggleSelection = issue => {
  selectedIssue.value =
    selectedIssue.value === issue ? null : issue
}

const editForm = ref({
  tracking: '',
  remarks: '',
  status: '',
})

const openEdit = () => {
  editForm.value = {
    tracking: selectedIssue.value.tracking,
    remarks: selectedIssue.value.remarks,
    status: selectedIssue.value.status,
  }
  showEditModal.value = true
}

const closeEdit = () => {
  showEditModal.value = false
}

const updateIssue = () => {
  selectedIssue.value.remarks = editForm.value.remarks
  selectedIssue.value.status = editForm.value.status
  closeEdit()
}

const openFilter = () => {
  showFilterPopup.value = true
}

const closeFilter = () => {
  showFilterPopup.value = false
}

const clearFilter = () => {
  filterStatus.value.Resolved = false
  filterStatus.value.Unresolved = false
}
</script>

<style scoped>
.page {
  padding: 24px;
  background: #f5f6f8;
  min-height: 100vh;
}

.card {
  background: #fff;
  border-radius: 10px;
  padding: 20px;
  color: #000;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  font-size: 25px;
}

.controls {
  display: flex;
  justify-content: space-between;
  margin-bottom: 12px;
}

.controls input {
  background: #ffffff;
  color: #000000;
  padding: 8px 12px;
  border-radius: 4px;
  border: 1px solid #000;
  width: 240px;
}

table {
  width: 100%;
  border-collapse: collapse;
  table-layout: auto;
}

th,
td {
  padding: 12px;
  border-bottom: 1px solid #eaeaea;
  text-align: center;
}

.remarks {
  text-align: left;
  white-space: normal;
  word-break: break-word;
}

.status {
  padding: 6px 14px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 500;
}

.status.resolved {
  background: #5fbf8f;
  color: #fff;
}

.status.unresolved {
  background: #c0392b;
  color: #fff;
}

tr.selected td {
  background: #e6e6e6;
}

.btn {
  padding: 8px 14px;
  border: 1px solid #000;
  border-radius: 6px;
  background: #fff;
  cursor: pointer;
  color: #000;
}

.btn.primary {
  background: #888b8e;
  color: #fff;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.modal-backdrop,
.filter-backdrop {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.3);
  backdrop-filter: blur(4px);
  z-index: 999;
  color: #000;
}

.modal {
  background: #fff;
  padding: 30px;
  width: 345px;
  border-radius: 8px;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  color: #000;
  border: 2px solid #000;
}

.modal-close {
  position: absolute;
  top: 10px;
  right: 12px;
  background: none;
  border: none;
  font-size: 22px;
  cursor: pointer;
  color: #000 !important;
}

.filter-popup {
  position: absolute;
  top: 180px;
  right: 80px;
  background: #fff;
  border: 1px solid #000;
  padding: 16px;
  width: 280px;
  border-radius: 8px;
}

.filter-group {
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 40px;
  font-size: 13px;
  align-items: center;
}

.filter-actions {
  display: flex;
  justify-content: center;
  gap: 18px;
  margin-top: 20px;
}

.filter-actions .btn {
  padding: 6px 14px;
  font-size: 13px;
}

.filter-popup h4 {
  margin: 8px 0;
  font-size: 20px;
}

.edit-modal {
  width: 360px;
}

.edit-modal h3 {
  text-align: center;
  margin-bottom: 18px;
}

.edit-modal .form-group {
  margin-bottom: 14px;
}

.edit-modal input,
.edit-modal textarea {
  width: 100%;
  padding: 8px;
  background: #f0f0f0;
  border: 1px solid #000;
  border-radius: 4px;
  font-size: 14px;
}

.edit-modal textarea {
  resize: vertical;
}

.readonly-box {
  padding: 8px;
  background: #f0f0f0;
  border: 1px solid #000;
  border-radius: 4px;
  font-size: 14px;
}

.status-options {
  display: flex;
  justify-content: center;
  gap: 30px;
  border: 1px solid #000;
  padding: 10px;
  border-radius: 4px;
}

.status-options label {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 13px;
}

.update-btn {
  width: 160px;
  padding: 10px 0;
}

.remarks-textbox {
  color: #000000;
  background: #f0f0f0;
  border: 1px solid #000;
  border-radius: 4px;
  padding: 8px;
  font-size: 14px;
  width: 100%;
}

.form-actions {
  display: flex;
  justify-content: center;
  margin-top: 22px;
}

.table-footer {
  padding: 0 45px;
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
  color: #000;
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
  margin: 0 8px;
  white-space: nowrap;
  position: relative;
  left: 30%;
}
</style>
