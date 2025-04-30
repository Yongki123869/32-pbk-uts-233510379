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
.container {
  max-width: 500px;
  margin: 2rem auto;
  padding: 2rem;
  border: 1px solid #ddd;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
  text-align: center;
  font-family: Arial, sans-serif;
}

h1 {
  margin-bottom: 1rem;
}

.form {
  display: flex;
  justify-content: center;
  margin-bottom: 1rem;
}

.filter {
  margin-bottom: 1rem;
}

input[type="text"] {
  padding: 0.5rem;
  width: 60%;
  margin-right: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 0.5rem 1rem;
  background-color: #42b883;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #369b72;
}

.list {
  list-style: none;
  padding: 0;
  text-align: left;
}

.activity-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}

.remove-btn {
  background-color: #ff5f5f;
  border: none;
  padding: 0.3rem 0.7rem;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

.remove-btn:hover {
  background-color: #e04848;
}

/* Style untuk menandakan kegiatan yang selesai */
.completed {
  text-decoration: line-through;
  color: gray;
}
</style>
