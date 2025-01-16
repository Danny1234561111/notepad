<template>
    <div class="kart-component">
      <div class="kart">
        <p class="error" v-if="error">{{ error }}</p>
        <textarea v-model="userName" placeholder="Введи Задачу" class="kart-input"></textarea>
        <select name="variant" v-model="userVariant" class="kart-select">
          <option value="small">😴Малый приоритет</option>
          <option value="medium">😲Средний приоритет</option>
          <option value="hard">⚡Высокий приоритет</option>
        </select>
        <button @click="submit" class="kart-button">Отправить</button>
        <button @click="$emit('close')" class="kart-button">Отмена</button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      onSubmit: {
        type: Function,
        required: true,
      },
      initialTask: {
        type: Object,
        default: null,
      },
    },
    data() {
      return {
        error: "",
        userName: this.initialTask ? this.initialTask.name : "",
        userVariant: this.initialTask ? this.initialTask.variant : "small",
      };
    },
    watch: {
      initialTask: {
        handler(newTask) {
          this.userName = newTask ? newTask.name : "";
          this.userVariant = newTask ? newTask.variant : "small";
        },
        immediate: true,
      },
    },
    methods: {
      submit() {
        this.error = "";
        if (!this.userName.trim()) {
          this.error = "Имя не введено";
          return;
        }
        this.$emit("submit", {
          name: this.userName.trim(),
          variant: this.userVariant,
        });
        this.userName = "";
        this.userVariant = "small";
      },
    },
  };
  </script>
  
  <style scoped>
  .kart-component {
    top: 20px;
    right: 20px;
  }
  
  .kart {
    background-color: white;
    padding: 10px;
    border-radius: 5px;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.25);
      display: flex;
      flex-direction: column;
      gap: 10px;
  }
  
  .error {
    color: red;
  }
  .kart-input {
       padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      resize: vertical; 
      font-size: 1rem; 
          box-sizing: border-box;
  }
  
  .kart-select {
      padding: 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
      font-size: 1rem;
       box-sizing: border-box;
  }
  .kart-button {
    padding: 10px 15px;
      background-color: #4CAF50;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
       font-size: 1rem;
  }
  </style>