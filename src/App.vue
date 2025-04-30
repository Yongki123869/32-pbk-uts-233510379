<script setup>
import { ref, onMounted, watch } from 'vue'

// State input dan daftar kegiatan
const newActivity = ref('')
const activities = ref([])

// Ambil data dari localStorage saat halaman dimuat
onMounted(() => {
  const saved = localStorage.getItem('activities')
  if (saved) {
    activities.value = JSON.parse(saved)
  }
})

// Simpan ke localStorage setiap kali activities berubah
watch(activities, (val) => {
  localStorage.setItem('activities', JSON.stringify(val))
}, { deep: true })

// Tambahkan kegiatan baru
function addActivity() {
  if (newActivity.value.trim()) {
    activities.value.push(newActivity.value.trim())
    newActivity.value = ''
  }
}
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

    <ul class="list">
      <li v-for="(activity, index) in activities" :key="index">
        {{ index + 1 }}. {{ activity }}
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

input {
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

.list li {
  padding: 0.5rem;
  border-bottom: 1px solid #eee;
}
</style>
