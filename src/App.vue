<script setup>
import { ref, computed, onMounted } from 'vue';

const daftarTugas = ref([
  { nama: "Siapkan bahan masakan utama", selesai: false },
  { nama: "Pantau stok bahan harian", selesai: true },
  { nama: "Koordinasi dengan staf dapur", selesai: false },
  { nama: "Inspeksi kebersihan dapur & ruang makan", selesai: true }
]);

const tugasBaru = ref("");
const inputTugas = ref(null);

const sedangEdit = ref(false);
const indeksEdit = ref(null);
const tampilkanSelesai = ref(true);

const tambahTugas = () => {
  const bersih = tugasBaru.value.trim();
  if (bersih) {
    daftarTugas.value.push({ nama: bersih, selesai: false });
    tugasBaru.value = "";
    inputTugas.value?.focus();
  }
};

const hapusTugas = (indeks) => {
  daftarTugas.value.splice(indeks, 1);
  batalEdit();
};

const editTugas = (indeks) => {
  tugasBaru.value = daftarTugas.value[indeks].nama;
  sedangEdit.value = true;
  indeksEdit.value = indeks;
  inputTugas.value?.focus();
};

const perbaruiTugas = () => {
  const bersih = tugasBaru.value.trim();
  if (indeksEdit.value !== null && bersih) {
    daftarTugas.value[indeksEdit.value].nama = bersih;
    batalEdit();
  }
};

const batalEdit = () => {
  tugasBaru.value = "";
  sedangEdit.value = false;
  indeksEdit.value = null;
};

const tugasTersaring = computed(() =>
  tampilkanSelesai.value
    ? daftarTugas.value
    : daftarTugas.value.filter(tugas => !tugas.selesai)
);

onMounted(() => {
  inputTugas.value?.focus();
});
</script>

<template>
  <div id="app">
    <div class="container">
      <div class="card">
        <h2>🍽 Agenda Harian Usaha Rumah Makan</h2>

        <label class="toggle-show">
          <input type="checkbox" v-model="tampilkanSelesai" />
          Tampilkan yang sudah selesai
        </label>

        <table class="task-table">
          <thead>
            <tr>
              <th>No</th>
              <th>Tugas</th>
              <th>Status</th>
              <th>Aksi</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(tugas, indeks) in tugasTersaring" :key="indeks">
              <td>{{ indeks + 1 }}</td>
              <td :class="{ done: tugas.selesai }">{{ tugas.nama }}</td>
              <td>
                <input type="checkbox" v-model="tugas.selesai" />
              </td>
              <td>
                <button @click="editTugas(indeks)">Edit</button>
                <button class="delete-btn" @click="hapusTugas(indeks)">Hapus</button>
              </td>
            </tr>
          </tbody>
        </table>

        <div class="input-group">
          <input
            ref="inputTugas"
            v-model="tugasBaru"
            type="text"
            placeholder="Tambahkan agenda rumah makan"
          />
          <button v-if="sedangEdit" @click="perbaruiTugas">Perbarui</button>
          <button v-else @click="tambahTugas">Tambah</button>
          <button v-if="sedangEdit" class="cancel-btn" @click="batalEdit">Batal</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
html,
body,
#app {
  margin: 0;
  padding: 0;
  width: 100%;
  min-height: 100vh;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  background: #fffaf2;
  color: #2e2e2e;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.container {
  flex: 1;
  display: flex;
  justify-content: center;
  padding: 40px 20px;
  box-sizing: border-box;
}

.card {
  width: 100%;
  max-width: 600px;
  background: #fff3e0;
  border-radius: 16px;
  padding: 30px 25px;
  box-shadow: 0 4px 16px rgba(255, 152, 0, 0.3);
  display: flex;
  flex-direction: column;
}

h2 {
  color: #ff6f00;
  text-align: center;
  margin-bottom: 25px;
}

.toggle-show {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
  font-size: 15px;
  color: #6d4c41;
  gap: 8px;
}

.task-table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.task-table th,
.task-table td {
  border: 1px solid #ffcc80;
  padding: 10px;
  text-align: left;
}

.task-table th {
  background-color: #ffe0b2;
  color: #bf360c;
}

.done {
  text-decoration: line-through;
  color: #bdbdbd;
}

.input-group {
  display: flex;
  gap: 10px;
  margin-top: 20px;
  flex-wrap: wrap;
}

input[type="text"] {
  flex: 1;
  padding: 12px;
  border: 1px solid #ffa726;
  border-radius: 8px;
  font-size: 15px;
  background-color: #fff8e1;
  color: #212529;
}

button {
  padding: 10px 16px;
  font-size: 14px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  background-color: #ff9800;
  color: white;
  transition: background 0.3s;
}

button:hover {
  background-color: #fb8c00;
}

.cancel-btn {
  background-color: #ffd54f;
  color: #4e342e;
}

.delete-btn {
  background-color: #ef5350;
}
</style>