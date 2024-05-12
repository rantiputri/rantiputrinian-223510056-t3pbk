<template>
  <div class="app" style="background-color: #ffe6f3; padding: 40px; border-radius: 10px;">
    <h1 style="color: #ff69b4; font-size: 36px;">To-Do List</h1>
    <!-- Form untuk menambahkan tugas baru -->
    <div class="add-task-form">
      <input v-model="newTaskDescription" type="text" placeholder="Tambahkan tugas baru" style="border: 2px solid #ff69b4; font-size: 18px;">
      <select v-model="newTaskCategory" style="border: 2px solid #ff69b4; font-size: 18px;">
        <option value="">Pilih Kategori</option>
        <option v-for="category in categories" :key="category">{{ category }}</option>
      </select>
      <button @click="addTask" style="background-color: #ff69b4; color: white; border: none; font-size: 18px; cursor: pointer;">Tambah</button>
    </div>

    <!-- Filter berdasarkan kategori -->
    <div class="category-filter">
      <select v-model="selectedCategory" style="border: 2px solid #ff69b4; font-size: 18px;">
        <option value="">Semua Kategori</option>
        <option v-for="category in categories" :key="category">{{ category }}</option>
      </select>
    </div>

    <!-- Daftar tugas -->
    <div class="task-list" style="background-color: #fff; padding: 20px; border-radius: 10px;">
      <div v-if="filteredTasks.length > 0">
        <h2 style="color: #ff69b4;">Daftar Tugas:</h2>
        <ul>
          <li v-for="(task, index) in filteredTasks" :key="index" style="margin-bottom: 10px;">
            <!-- Checkbox untuk menandai tugas sebagai selesai -->
            <input type="checkbox" v-model="task.isCompleted" @change="completeTask(index)">
            <span :class="{ 'completed-task': task.isCompleted }" style="color: #ff69b4; font-size: 18px;">{{ task.description }}</span>
            <span class="task-category" style="font-size: 18px;">{{ task.category }}</span>
            <!-- Tombol untuk menghapus tugas -->
            <button @click="deleteTask(index)" style="background-color: #ff69b4; color: white; border: none; font-size: 18px; cursor: pointer; margin-right: 10px;">Hapus</button>
          </li>
        </ul>
      </div>
      <div v-else>
        <p style="font-size: 18px;">Tidak ada tugas</p>
      </div>
    </div>

    <!-- Statistik jumlah tugas belum selesai -->
    <div class="task-statistics" style="margin-top: 20px;">
      <p style="color: #ff69b4;font-size: 18px;">Total Tugas Belum Selesai: {{ totalIncompleteTasks }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTaskDescription: '',
      newTaskCategory: '',
      selectedCategory: '',
      categories: ['Pekerjaan', 'Belajar', 'Rumah Tangga', 'Olahraga'],
      tasks: JSON.parse(localStorage.getItem('tasks')) || []
    };
  },
  computed: {
    filteredTasks() {
      return this.tasks.filter(task => {
        if (this.selectedCategory) {
          return task.category === this.selectedCategory;
        } else {
          return true;
        }
      });
    },
    totalIncompleteTasks() {
      return this.filteredTasks.filter(task => !task.isCompleted).length;
    }
  },
  watch: {
    tasks: {
      handler(newTasks) {
        localStorage.setItem('tasks', JSON.stringify(newTasks));
      },
      deep: true
    }
  },
  methods: {
    addTask() {
      if (this.newTaskDescription.trim() !== '' && this.newTaskCategory !== '') {
        this.tasks.push({
          description: this.newTaskDescription,
          category: this.newTaskCategory,
          isCompleted: false
        });
        this.newTaskDescription = '';
        this.newTaskCategory = '';
      }
    },
    completeTask(index) {
      this.tasks[index].isCompleted = !this.tasks[index].isCompleted;
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    }
  }
};
</script>

<style scoped>
.app {
  font-family: Arial, sans-serif;
  max-width: 800px;
  margin: 0 auto;
  padding: 40px;
}
</style>
