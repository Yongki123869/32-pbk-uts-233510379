<script setup>
import { ref, onMounted, watch, computed } from 'vue'

const newActivity = ref('')
const activities = ref([])
const showUncompletedOnly = ref(false) // Toggle filter kegiatan

// Ambil data dari localStorage saat halaman dimuat
onMounted(() => {
  const saved = localStorage.getItem('activities')
  if (saved) {
    activities.value = JSON.parse(saved)
  }
})

// Simpan ke localStorage setiap kali daftar kegiatan berubah
watch(activities, (val) => {
  localStorage.setItem('activities', JSON.stringify(val))
}, { deep: true })

// Tambah kegiatan baru
function addActivity() {
  if (newActivity.value.trim()) {
    activities.value.push({ name: newActivity.value.trim(), completed: false })
    newActivity.value = ''
  }
}

// Hapus kegiatan berdasarkan index
function removeActivity(index) {
  activities.value.splice(index, 1)
}

// Daftar kegiatan berdasarkan filter
const filteredActivities = computed(() =>
  showUncompletedOnly.value
    ? activities.value.filter(activity => !activity.completed)
    : activities.value
)
</script>

<template>
  <div class="container">
    <h1>Daftar Kegiatan Harian</h1>

    <div class="form">
      <input
        v-model="newActivity"
        @keyup.enter="addActivity"
        type="text"
        placeholder="Masukkan kegiatan baru"
      />
      <button @click="addActivity">Tambah</button>
    </div>

    <!-- Checkbox filter -->
    <div class="filter">
      <label>
        <input type="checkbox" v-model="showUncompletedOnly" />
        Tampilkan hanya yang belum selesai
      </label>
    </div>

    <ul class="list">
      <li
        v-for="(activity, index) in filteredActivities"
        :key="index"
        class="activity-item"
      >
        <input
          type="checkbox"
          v-model="activity.completed"
        />
        <span :class="{ completed: activity.completed }">
          {{ index + 1 }}. {{ activity.name }}
        </span>
        <button class="remove-btn" @click="removeActivity(activities.indexOf(activity))">
          Hapus
        </button>
      </li>
    </ul>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

body {
  background: linear-gradient(145deg, #e0f7fa, #f1f8e9);
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 600px;
  margin: 3rem auto;
  padding: 2rem 2.5rem;
  background-color: #ffffff;
  border-radius: 20px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
  text-align: center;
  transition: all 0.3s ease-in-out;
}

h1 {
  font-size: 1.8rem;
  color: #2e7d32;
  margin-bottom: 1.5rem;
  font-weight: 600;
}

.form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
  justify-content: center;
}

input[type="text"] {
  padding: 0.6rem 1rem;
  flex: 1;
  border: 2px solid #a5d6a7;
  border-radius: 12px;
  outline: none;
  transition: 0.3s;
}

input[type="text"]:focus {
  border-color: #66bb6a;
  box-shadow: 0 0 0 2px rgba(102, 187, 106, 0.2);
}

button {
  padding: 0.6rem 1.2rem;
  background: #66bb6a;
  color: white;
  font-weight: 600;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  transition: background 0.3s;
}

button:hover {
  background: #4caf50;
}

.filter {
  margin-bottom: 1.2rem;
  color: #333;
  font-size: 0.95rem;
  text-align: left;
}

.filter input {
  margin-right: 0.5rem;
  accent-color: #66bb6a;
}

.list {
  list-style: none;
  padding: 0;
  margin-top: 1rem;
}

.activity-item {
  background-color: #f9fbe7;
  margin-bottom: 0.8rem;
  padding: 0.75rem 1rem;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: transform 0.2s;
  box-shadow: 0 4px 8px rgba(102, 187, 106, 0.1);
}

.activity-item:hover {
  transform: scale(1.01);
}

.activity-item input[type="checkbox"] {
  margin-right: 0.75rem;
  transform: scale(1.2);
  accent-color: #66bb6a;
}

.activity-item span {
  flex: 1;
  font-size: 1rem;
  color: #333;
}

.completed {
  text-decoration: line-through;
  color: #9e9e9e;
}

.remove-btn {
  background-color: #ef5350;
  border: none;
  padding: 0.4rem 0.9rem;
  color: white;
  font-size: 0.85rem;
  border-radius: 10px;
  cursor: pointer;
  transition: background 0.3s;
}

.remove-btn:hover {
  background-color: #d32f2f;
}
</style>
