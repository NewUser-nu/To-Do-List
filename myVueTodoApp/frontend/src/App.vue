<template>
  <div id="app">
    <h1>To-Do List</h1>
    
    <!-- Форма для добавления новой задачи -->
    <div class="task-form">
      <input v-model="newTask.text" placeholder="Введите новую задачу" @input="validateInput" />
      <input type="date" v-model="newTask.dueDate" />
      <input type="time" v-model="newTask.dueTime" />
      <select v-model="newTask.priority">
        <option disabled value="">Приоритет</option>
        <option>Низкий</option>
        <option>Средний</option>
        <option>Высокий</option>
      </select>
      <button @click="addTask" :disabled="isAddDisabled">Добавить задачу</button>
    </div>
    
    <!-- Сообщение об ошибке -->
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>
    
    <!-- Список задач -->
    <div>
      <h2>Не выполненные задачи</h2>
      <ul>
        <li v-for="(task, index) in pendingTasks" :key="index" :class="{ completed: task.completed }">
          <div>
            <strong>{{ task.text }}</strong>
            <span v-if="task.dueDate || task.dueTime"> - {{ task.dueDate }} {{ task.dueTime }}</span>
            <span v-if="task.priority"> - <span :class="priorityClass(task.priority)">{{ task.priority }}</span></span>
          </div>
          <div>
            <span @click="toggleTask(index)" :class="task.completed ? 'complete-icon' : 'incomplete-icon'">
              {{ task.completed ? '✔️' : '❌' }}
            </span>
            <button @click="confirmDeleteTask(index)" class="delete-button">🗑️</button>
          </div>
        </li>
      </ul>
    </div>
    
    <div>
      <h2>Выполненные задачи</h2>
      <ul>
        <li v-for="(task, index) in completedTasks" :key="index" :class="{ completed: task.completed }">
          <div>
            <strong>{{ task.text }}</strong>
            <span v-if="task.dueDate || task.dueTime"> - {{ task.dueDate }} {{ task.dueTime }}</span>
            <span v-if="task.priority"> - <span :class="priorityClass(task.priority)">{{ task.priority }}</span></span>
          </div>
          <div>
            <span @click="toggleTask(index)" :class="task.completed ? 'incomplete-icon' : 'complete-icon'">
              {{ task.completed ? '❌' : '✔️' }}
            </span>
            <button @click="confirmDeleteTask(index)" class="delete-button">🗑️</button>
          </div>
        </li>
      </ul>
    </div>
    
    <!-- Модальное окно подтверждения удаления -->
    <div v-if="showConfirmModal" class="modal">
      <div class="modal-content">
        <p>Вы уверены, что хотите удалить эту задачу?</p>
        <button @click="deleteTask">Удалить</button>
        <button @click="cancelDelete">Отмена</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: {
        text: "",
        dueDate: "",
        dueTime: "",
        priority: ""
      },
      tasks: [],
      errorMessage: "",
      taskToDelete: null,
      showConfirmModal: false
    };
  },
  computed: {
    isAddDisabled() {
      return this.newTask.text.trim() === "";
    },
    pendingTasks() {
      return this.tasks.filter(task => !task.completed);
    },
    completedTasks() {
      return this.tasks.filter(task => task.completed);
    }
  },
  methods: {
    validateInput() {
      if (this.newTask.text.trim() === "") {
        this.errorMessage = "Задача не может быть пустой.";
      } else {
        this.errorMessage = "";
      }
    },
    addTask() {
      if (this.newTask.text.trim() !== "") {
        this.tasks.push({
          text: this.newTask.text,
          dueDate: this.newTask.dueDate,
          dueTime: this.newTask.dueTime,
          priority: this.newTask.priority,
          completed: false
        });
        this.newTask = { text: "", dueDate: "", dueTime: "", priority: "" };
        this.errorMessage = "";
      }
    },
    toggleTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
    },
    confirmDeleteTask(index) {
      this.taskToDelete = index;
      this.showConfirmModal = true;
    },
    deleteTask() {
      if (this.taskToDelete !== null) {
        this.tasks.splice(this.taskToDelete, 1);
        this.taskToDelete = null;
        this.showConfirmModal = false;
      }
    },
    cancelDelete() {
      this.taskToDelete = null;
      this.showConfirmModal = false;
    },
    priorityClass(priority) {
      switch (priority) {
        case 'Низкий':
          return 'low-priority';
        case 'Средний':
          return 'medium-priority';
        case 'Высокий':
          return 'high-priority';
        default:
          return '';
      }
    }
  }
};

</script>

<style>
/* Стили для улучшения интерфейса */
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
}

.task-form {
  margin-bottom: 20px;
}

input, select {
  padding: 10px;
  font-size: 16px;
  margin-right: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 5px;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #45a049;
}

ul {
  list-style-type: none;
  padding: 0;
  margin-top: 20px;
  width: 320px;
  margin: 0 auto;
}

li {
  text-align: left;
  padding: 10px;
  border-bottom: 1px solid #ddd;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

li.completed {
  text-decoration: line-through;
  color: #999;
}

.complete-icon {
  color: green;
  cursor: pointer;
  font-size: 20px;
}

.incomplete-icon {
  color: red;
  cursor: pointer;
  font-size: 20px;
}

.delete-button {
  background: none;
  border: none;
  cursor: pointer;
  color: #ff4d4d;
  font-size: 20px;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background: white;
  padding: 20px;
  border-radius: 5px;
  text-align: center;
}

.modal-content button {
  margin: 10px;
}

.low-priority {
  color: green;
}

.medium-priority {
  color: orange;
}

.high-priority {
  color: red;
}
</style>
