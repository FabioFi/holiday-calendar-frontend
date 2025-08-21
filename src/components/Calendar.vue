<template>
  <div class="calendar">
    <div class="calendar-header">
      <button @click="prevMonth">&lt;</button>
      <span>{{ monthName }} {{ year }}</span>
      <button @click="nextMonth">&gt;</button>
    </div>
    <div class="calendar-grid">
      <div class="day-header" v-for="d in days" :key="d">{{ d }}</div>
      <div
        v-for="cell in calendarCells"
        :key="cell.key"
        class="day-cell"
        :class="{ today: cell.isToday, hasHoliday: cell.holiday }"
        @click="cell.date && openAddHoliday(cell.date)"
      >
        <span class="date-number">{{ cell.date ? cell.date.getDate() : '' }}</span>
        <div v-if="cell.holiday" class="holiday-list">
          <div v-for="h in cell.holiday" :key="h.id" class="holiday-item">
            🎉 {{ h.name }}
            <button
              class="delete-btn"
              @click.stop="deleteHoliday(h.id)"
              title="Delete holiday"
              aria-label="Delete holiday"
            >✖</button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="showModal" class="modal-overlay" @click.self="closeModal">
      <div class="modal">
        <h2>Add Holiday</h2>
        <p>Date: <strong>{{ modalDateStr }}</strong></p>
        <form @submit.prevent="submitHoliday">
          <input v-model="holidayName" placeholder="Your name..." required />
          <button type="submit">Add</button>
          <button type="button" @click="closeModal">Cancel</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

// const API_URL = 'https://YOUR_RENDER_BACKEND_URL' // <-- change to your Render backend URL
const API_URL = 'https://holiday-calendar-backend.onrender.com'

const today = new Date()
const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat']

const year = ref(today.getFullYear())
const month = ref(today.getMonth())

const holidays = ref([])

const showModal = ref(false)
const modalDate = ref(null)
const modalDateStr = computed(() =>
  modalDate.value
    ? modalDate.value.toISOString().slice(0, 10)
    : ''
)
const holidayName = ref('')

// Fetch holidays from backend
const fetchHolidays = async () => {
  const { data } = await axios.get(`${API_URL}/holidays`)
  holidays.value = data
}

// Add new holiday
const submitHoliday = async () => {
  await axios.post(`${API_URL}/holidays`, {
    date: modalDateStr.value,
    name: holidayName.value.trim(),
  })
  holidayName.value = ''
  closeModal()
  fetchHolidays()
}

// Delete holiday
const deleteHoliday = async (id) => {
  await axios.delete(`${API_URL}/holidays/${id}`)
  fetchHolidays()
}

// Calendar grid logic
const monthName = computed(() =>
  new Date(year.value, month.value).toLocaleString('default', { month: 'long' })
)

const calendarCells = computed(() => {
  const firstDay = new Date(year.value, month.value, 1)
  const lastDay = new Date(year.value, month.value + 1, 0)
  const startDay = firstDay.getDay()
  const daysInMonth = lastDay.getDate()

  const cells = []
  // Previous month's empty cells
  for (let i = 0; i < startDay; i++) {
    cells.push({ key: `empty-${i}` })
  }
  // Current month's days
  for (let d = 1; d <= daysInMonth; d++) {
    const date = new Date(year.value, month.value, d)
    const dateStr = date.toISOString().slice(0, 10)
    const holiday = holidays.value.filter(h => h.date === dateStr)
    cells.push({
      key: dateStr,
      date,
      isToday:
        date.getFullYear() === today.getFullYear() &&
        date.getMonth() === today.getMonth() &&
        date.getDate() === today.getDate(),
      holiday: holiday.length ? holiday : null,
    })
  }
  // Fill to complete weeks
  while (cells.length % 7 !== 0) {
    cells.push({ key: `empty-end-${cells.length}` })
  }
  return cells
})

// Modal logic
const openAddHoliday = (date) => {
  modalDate.value = date
  showModal.value = true
}
const closeModal = () => {
  showModal.value = false
  modalDate.value = null
  holidayName.value = ''
}

// Month navigation
const prevMonth = () => {
  if (month.value === 0) {
    month.value = 11
    year.value--
  } else {
    month.value--
  }
}
const nextMonth = () => {
  if (month.value === 11) {
    month.value = 0
    year.value++
  } else {
    month.value++
  }
}

// Initial fetch
onMounted(() => {
  fetchHolidays()
})

</script>

<style scoped>
.calendar {
  background: #222;
  padding: 1rem;
  border-radius: 10px;
}

.calendar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  font-size: 1.2rem;
}

.calendar-header button {
  background: #444;
  color: #fff;
  border: none;
  padding: 0.4rem 1rem;
  border-radius: 6px;
  cursor: pointer;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 6px;
}

.day-header {
  text-align: center;
  font-weight: bold;
  padding: 0.2rem 0;
  background: #333;
  border-radius: 4px;
}

.day-cell {
  min-height: 66px;
  background: #222;
  border-radius: 8px;
  text-align: left;
  padding: 5px;
  cursor: pointer;
  position: relative;
  transition: background 0.2s;
}

.day-cell.today {
  border: 2px solid #3fa7ff;
}

.day-cell.hasHoliday {
  background: #2a3;
  color: #fff;
}

.date-number {
  font-size: 1.1rem;
  font-weight: bold;
}

.holiday-list {
  margin-top: 6px;
}

.holiday-item {
  background: #fff2;
  margin: 2px 0;
  padding: 2px 5px;
  border-radius: 5px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 0.95rem;
}

.delete-btn {
  background: none;
  border: none;
  color: #f88;
  cursor: pointer;
  font-size: 1rem;
  margin-left: 6px;
  border-radius: 2px;
  padding: 0 2px;
}

.modal-overlay {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: #000a;
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 10;
}

.modal {
  background: #333;
  padding: 2rem;
  border-radius: 10px;
  min-width: 260px;
  box-shadow: 0 2px 8px #0006;
}

.modal input {
  padding: 0.5rem;
  width: 100%;
  margin-bottom: 1rem;
  border-radius: 6px;
  border: 1px solid #555;
  background: #222;
  color: #fff;
}

.modal button {
  background: #444;
  color: #fff;
  border: none;
  padding: 0.4rem 1rem;
  border-radius: 6px;
  cursor: pointer;
  margin-right: 0.5rem;
  margin-top: 0.5rem;
}
</style>