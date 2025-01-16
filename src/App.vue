<template>
  <div class="app">
    <div class="note-list">
      <div class="add-button-container2">
        <button @click="retry()" class="add-button">Сброс</button>
      </div>
      <div class="add-button-container">
        <button @click="handlePlusClick()" class="add-button">+</button>
      </div>
      <div class="notes">
        <div v-for="(el, index) in users" :key="index" class="note">
          <input type="checkbox" v-model="el.checked" @change="updatePriority(el)" />
          <p :style="{ textDecoration: el.checked ? 'line-through' : 'none' }">
            <span v-if="el.prevVariant === 'hard'">⚡</span>
            <span v-else-if="el.prevVariant === 'medium'">😲</span>
            <span v-else>😴</span>
            {{ el.name }}
          </p>
          <button @click="editTask(el, index)" class="edit-button">Редактировать</button>
          <button @click="deleteTask(index)" class="delete-button">Удалить</button>
        </div>
      </div>
    </div>
    <Kart v-if="showKart" :onSubmit="handleTaskSubmit" :initialTask="editingTask" @close="closeKart" class="kart-component"/>
  </div>
</template>

<script>
import Kart from './components/Kart.vue';
if (localStorage.getItem('users')===null){
  localStorage.setItem('users',JSON.stringify(defaultArray))
}
export default {
  components: { Kart },
  data() {
    return {
      users: JSON.parse(localStorage.getItem('users')),
      showKart: false,
      editingTask: null,
      editingIndex: null,
    };
  },
  methods: {
    handlePlusClick() {
      if (this.editingTask != null){
        this.showKart = true;
      }
      else{
        this.showKart = !this.showKart;
      }
      this.editingTask = null;
      this.editingIndex = null;
    },
    retry(){
      localStorage.setItem('users',JSON.stringify([]))
      this.users=[]
    },
    closeKart() {
      this.showKart = false;
      this.editingTask = null;
      this.editingIndex = null;
    },
    editTask(task, index) {
      this.editingTask = { ...task };
      this.editingIndex = index;
      this.showKart = true;
    },
    deleteTask(index) {
      this.users.splice(index, 1);
      localStorage.setItem('users',JSON.stringify(this.users))
    },
    handleTaskSubmit(task) {
      if (this.editingIndex !== null) {
        this.users.splice(this.editingIndex, 1, { ...task, checked: false, prevVariant: task.variant });
         this.editingTask = null;
        this.editingIndex = null;
      } else {
        this.users.push({ ...task, checked: false, prevVariant: task.variant });
      }
      this.sortUsers();
      this.closeKart();
    },
    updatePriority(el) {
      if (el.checked) {
        el.variant = 'completed';
        this.sortUsers();
      } else {
        el.variant = el.prevVariant;
        this.sortUsers();
      }
    },
    sortUsers() {
      const priorityOrder = {
        hard: 4,
        medium: 3,
        small: 2,
        completed: 1,
      };
      this.users.sort((a, b) => priorityOrder[b.variant] - priorityOrder[a.variant]);
      localStorage.setItem('users',JSON.stringify(this.users))
    },
  },
};
</script>

<style scoped>
.app {
  display: flex;
  width: 100%;
  height: 100vh;
  padding: 20px;
  box-sizing: border-box;
  background-color: #f0f0f0;
}

.note-list {
  flex: 1;
  position: relative;
  display: flex;
  flex-direction: column;
  max-width: 600px;
  background-color: rgba(240, 240, 240, 0.9);
  padding: 10px;
  box-sizing: border-box;
  border-radius: 5px;
  border: 1px solid #ccc;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  margin-right: 20px;
}

.add-button-container {
  position: absolute;
  top: 10px;
  right: 10px;
}
.add-button-container2 {
  position: absolute;
  top: 10px;
  left: 10px;
}

.add-button {
  padding: 10px 15px;
  font-size: 1.2rem;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}


.notes {
  flex: 1;
  overflow-y: auto;
  padding: 10px;
  box-sizing: border-box;
  margin-right: 50px;
}

.note {
  display: flex;
  align-items: center;
  padding: 5px 0;
  border-bottom: 1px solid #eee;
}

.note p {
  margin-left: 10px;
  flex: 1;
}

.note button {
  margin-left: 5px;
  padding: 5px;
  border: 1px solid #ccc;
  cursor: pointer;
  border-radius: 5px;
}

.edit-button {
  background-color: #4CAF50;
  color: white;
}

.delete-button {
  background-color: #f44336;
  color: white;
}

.note p span {
  margin-right: 5px;
}
.kart-component {
  width: 300px;
    /* Убрали position: absolute; и right */
}
</style>