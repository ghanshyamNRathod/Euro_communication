<template>
  <div class="bg-gradient-to-b from-gray-100 to-gray-300 min-h-screen py-8">
    <div class="container mx-auto p-4">
      <h1 class="text-3xl font-semibold text-center text-blue-600 mb-8">To-Do List</h1>
      <div class="mb-4">
        <div class="flex flex-col md:flex-row space-y-2 md:space-y-0 md:space-x-2">
          <div class="w-full md:w-2/3 lg:w-1/2">
            <div class="flex space-x-2">
              <input v-model="newTask.text" @keyup.enter="addTask" placeholder="Add a new task"
                     class="flex-grow p-2 border-b-2 border-blue-600 text-gray-700 focus:outline-none focus:border-blue-600" />
              <input type="date" v-model="newTask.dueDate" placeholder="Due Date"
                     class="p-2 border-b-2 border-blue-600 text-gray-700 focus:outline-none focus:border-blue-600" />
              <button @click="addTask"
                      class="px-4 py-2 text-white bg-blue-600 rounded-lg hover:bg-blue-700 focus:outline-none">
                Add
              </button>
            </div>
            <div class="text-red-600 mt-2">{{ taskTextError }}</div>
            <div class="text-red-600 mt-2">{{ dueDateError }}</div>
          </div>
          <div class="mt-2 md:mt-0">
            <button @click="showPendingTasks"
                    :class="{ 'bg-blue-600 hover:bg-blue-700': !showPending, 'bg-gray-400': showPending }"
                    class="px-4 py-2 text-white rounded-lg focus:outline-none">View Pending
            </button>
            <button @click="showCompletedTasks"
                    :class="{ 'bg-blue-600 hover:bg-blue-700': showCompleted, 'bg-gray-400': !showCompleted }"
                    class="px-4 py-2 text-white rounded-lg focus:outline-none">View Completed
            </button>
          </div>
        </div>
      </div>
      <div class="space-y-4">
        <div v-if="tasks.length === 0" class="text-gray-600 text-lg text-center">No tasks yet.</div>
        <div v-else>
          <div v-for="(task, index) in filteredTasks" :key="index">
            <todo-item :task="task" @toggle="toggleTask" @delete="deleteTask" @edit="editTask" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>



<script>
import TodoItem from "~/components/TodoItem.vue";

export default {
  components: {
    TodoItem,
  },
  data() {
    return {
      newTask: { text: "", dueDate: null },
      tasks: [],
      showPending: true,
      showCompleted: false,
      taskTextError: "", // Error message for task text
      dueDateError: "", // Error message for due date
    };
  },
  methods: {
    validateTaskText() {
      this.taskTextError = "";
      if (!this.newTask.text.trim()) {
        this.taskTextError = "Task text is required.";
        return false;
      }
      return true;
    },

    validateDueDate() {
      this.dueDateError = "";
      if (!this.newTask.dueDate) {
        this.dueDateError = "Due date is required.";
        return false;
      }
      return true;
    },

    validateForm() {
      const isTaskTextValid = this.validateTaskText();
      const isDueDateValid = this.validateDueDate();
      return isTaskTextValid && isDueDateValid;
    },
    addTask() {
      if (this.validateForm()) {
        this.tasks.push({ ...this.newTask, completed: false });
        this.newTask.text = "";
        this.newTask.dueDate = null;
      }
    },
    toggleTask() {
      this.$emit("toggle", this.task);
    },
    markAsComplete(task) {
      task.completed = true;
    },

    showPendingTasks() {
      this.showPending = true;
      this.showCompleted = false;
    },
    showCompletedTasks() {
      this.showCompleted = true;
      this.showPending = false;
    },

    deleteTask(task) {
      const index = this.tasks.indexOf(task);
      if (index !== -1) {
        this.tasks.splice(index - 1, 1);
      }
      this.$modal.hide("confirm-delete");
    },
    editTask(task) {
      // Task edit handled within the TodoItem component
    },
  },
  computed: {
    filteredTasks() {
      if (this.showPending) {
        return this.tasks.filter((task) => !task.completed);
      } else if (this.showCompleted) {
        return this.tasks.filter((task) => task.completed);
      }
    },
  },
};
</script>

<style>
/* Add your custom styles here */
</style>
