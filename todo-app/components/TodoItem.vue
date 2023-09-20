<template>
  <div
    class="bg-white shadow-lg rounded-lg p-4 flex justify-between items-center mb-2"
  >
    <div class="flex items-center space-x-4">
      <input
        type="checkbox"
        v-model="task.completed"
        @change="toggleTask"
        class="form-checkbox h-6 w-6 text-blue-600 focus:ring focus:ring-blue-400 focus:ring-offset-0"
      />
      <span
        :class="{
          'line-through text-gray-600': task.completed,
          'text-black': !task.completed,
          'text-red-600': isOverdue && !task.completed 
        }"
        @click="startEdit"
        >{{ task.text }}</span
      >
    </div>
    <div class="flex items-center space-x-4">
      <button
        @click="confirmDeleteTask(task)"
        class="text-red-600 hover:text-red-800"
      >
        Delete
      </button>
      <button @click="startEdit" class="text-blue-600 hover:text-blue-800">
        Edit
      </button>
      <button
        @click="markAsComplete"
        v-if="!task.completed"
        class="text-green-600 hover:text-green-800 focus:outline-none"
      >
        Mark Complete
      </button>
    </div>
    <modal name="confirm-delete">
      <div class="modal-content p-4">
        <p class="text-lg font-semibold text-red-600">
          Are you sure you want to delete this task?
        </p>
        <div class="flex space-x-4 mt-4">
          <button
            @click="deleteTask"
            class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 focus:outline-none"
          >
            Yes
          </button>
          <button
            @click="$modal.hide('confirm-delete')"
            class="px-4 py-2 bg-gray-400 text-gray-800 rounded-lg hover:bg-gray-500 focus:outline-none"
          >
            No
          </button>
        </div>
      </div>
    </modal>
    <modal name="edit-task">
      <div class="modal-content p-4">
        <p class="text-lg font-semibold text-blue-600">Edit Task</p>
        <input
          v-model="editedText"
          @keyup.enter="saveEdit"
          @blur="cancelEdit"
          ref="editInput"
          class="w-full p-2 border-b-2 border-blue-600 text-gray-700 focus:outline-none focus:border-blue-600 mt-2"
        />
        <input
          type="date"
          v-model="editedDueDate"
          @blur="saveDueDate"
          class="w-full p-2 border-b-2 border-blue-600 text-gray-700 focus:outline-none focus:border-blue-600 mt-2"
        />
        <div class="flex space-x-4 mt-4">
          <button
            @click="saveEdit"
            class="px-4 py-2 text-white bg-blue-600 rounded-lg hover:bg-blue-700 focus:outline-none"
          >
            Save
          </button>
          <button
            @click="cancelEdit"
            class="px-4 py-2 text-gray-600 bg-gray-300 rounded-lg hover:bg-gray-400 focus:outline-none"
          >
            Cancel
          </button>
        </div>
      </div>
    </modal>
  </div>
</template>


<script>
export default {
  props: {
    task: Object,
  },
  data() {
    return {
      editing: false,
      editedText: this.task.text,
      editedDueDate: this.task.dueDate,
    };
  },
  computed: {
    isOverdue() {
      const currentDate = new Date();
      const dueDate = new Date(this.task.dueDate);
      return currentDate > dueDate && !this.task.completed;
    },
    formattedDueDate() {
      return new Date(this.task.dueDate).toLocaleDateString();
    },
  },
  methods: {
    toggleTask() {
      this.$emit("toggle", this.task);
    },
    deleteTask() {
      this.$emit("delete", this.task);
    },
    markAsComplete() {
      this.task.completed = true;
    },
    startEdit() {
      this.editing = true;
      this.$modal.show("edit-task");
    },
    saveEdit() {
      if (this.editedText.trim() === "") {
        return; // Prevent saving empty task
      }
      this.task.text = this.editedText;
      this.task.dueDate = this.editedDueDate;
      this.editing = false;
      this.$emit("edit", this.task);
      this.$modal.hide("edit-task");
    },
    cancelEdit() {
      this.editing = false;
      this.editedText = this.task.text;
      this.editedDueDate = this.task.dueDate;
      this.$modal.hide("edit-task");
    },
    confirmDeleteTask(task) {
      this.$modal.show("confirm-delete");
      this.taskToDelete = task;
    },
    saveEditedTask(task) {
      // Update the task's properties here (text and dueDate)
      // You can use task.text and task.dueDate
      // ...
      this.$modal.show("edit-task");
    },
    saveDueDate() {
      // Due date is saved along with task text
      this.saveEdit();
    },
  },
};
</script>

<style scoped>
.completed {
  text-decoration: line-through;
}

.overdue {
  color: red;
}
</style>
