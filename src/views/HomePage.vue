<template>
  <div id="backgroundHome">
    <!-- <NavbarUser /> -->
    <button @click="showModal = true" class="add-button">Postar Tarefas</button>
    <ModalAction
      v-if="showModal"
      :taskData="selectedTask"
      @close="showModal = false"
      @task-created="refreshTasks"
      @task-updated="refreshTasks"
    />

    <AppListTasks
      ref="AppListTasks"
      @edit-task="openEditModal"
      @delete-task="openDeleteModal"
    />

    <ModalConfirm
      v-if="isConfirmDeleteModalOpen"
      :task="taskToDelete"
      @confirm="confirmDelete(taskToDelete.id)"
      @task-updated="refreshTasks"
      @close="isConfirmDeleteModalOpen = false"
    />
  </div>
</template>

<script>
import AppListTasks from "../components/AppListTasks.vue";
import ModalAction from "../components/ModalAction.vue";
import ModalConfirm from "../components/ModalConfirm.vue";

export default {
  name: "HomePage",
  components: {
    AppListTasks,
    ModalAction,
    ModalConfirm,
  },
  data() {
    return {
      showModal: false,
      isConfirmDeleteModalOpen: false,
      selectedTask: null,
      taskToDelete: null,
    };
  },
  methods: {
    refreshTasks() {
      this.$refs.AppListTasks.getTasks();
    },
    openEditModal(task) {
      this.selectedTask = task;
      this.showModal = true;
    },
    closeModal() {
      this.showModal = false;
      this.selectedTask = null;
    },
    openDeleteModal(task) {
      this.taskToDelete = task;
      this.isConfirmDeleteModalOpen = true;
    },
    async confirmDelete() {
      try {
        if (this.taskToDelete) {
          await this.deleteTask(this.taskToDelete.id);
          console.log("Excluindo task com ID:", this.taskToDelete.id);
        }
      } catch (error) {
        console.error("Erro ao excluir task:", error);
      } finally {
        this.isConfirmDeleteModalOpen = false;
        this.taskToDelete = null;
        this.refreshTasks();
      }
    },
    async deleteTask(taskId) {
      await this.$http.delete(
        `${process.env.VUE_APP_API_URL}/task/delete/${taskId}`,
      );
    },
  },
};
</script>
<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;

  background-color: beige;
}
#backgroundHome {
  background-color: beige;
  background-position: center;
  font-family: "Poppins", sans-serif;
  height: 100vh;
  width: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  position: relative;
}

.add-button {
  background-color: #0288d1;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.add-button:hover {
  background-color: #01579b;
}
</style>
