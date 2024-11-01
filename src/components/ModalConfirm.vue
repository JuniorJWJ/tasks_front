<template>
  <div class="modal">
    <div class="modal-content">
      <h3>Confirmar Exclusão</h3>
      <p>Tem certeza de que deseja excluir este task?</p>
      <div class="modal-actions">
        <button class="btn confirm" @click="confirmDelete">Confirmar</button>
        <button class="btn cancel" @click="$emit('close')">Cancelar</button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  props: {
    task: Object,
  },
  methods: {
    async confirmDelete() {
      console.log("id para ser excluido: " + this.task.id);
      
      try {
        if (this.task.id) {
          await axios.delete(
            `${process.env.VUE_APP_API_URL}/task/delete/${this.task.id}`,
          );
          this.$emit("task-updated");
        }
        this.closeModal();
      } catch (err) {
        console.error(err);
      }
    },
    closeModal() {
      this.$emit("close");
    },
  },
};
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  text-align: center;
}

.modal h3 {
  margin-bottom: 10px;
  color: #333;
}

.modal p {
  margin-bottom: 20px;
  color: #666;
}

.modal-actions {
  display: flex;
  justify-content: space-around;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}

.confirm {
  background-color: #f44336; /* Vermelho para confirmar exclusão */
  color: white;
}

.cancel {
  background-color: #0288d1; /* Azul para cancelar */
  color: white;
}

.btn:hover {
  opacity: 0.9; /* Efeito de hover */
}
</style>
