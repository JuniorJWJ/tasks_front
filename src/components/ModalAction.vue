<template>
  <div>
    <div class="modal-overlay" @click.self="closeModal">
      <div class="modal-content">
        <h2 class="titulo">
          {{ taskData ? "Editar Tarefa" : "Cadastrar Tarefa" }}
        </h2>
        <form @submit.prevent="onSubmit">
          <!-- Nome da Tarefa -->
          <div class="form-group">
            <label for="name">Nome da Tarefa</label>
            <input
              type="text"
              id="name"
              v-model="name"
              class="form-control"
              placeholder="Nome da tarefa"
              required
            />
            <div v-if="errorMessage" class="error-message">
              {{ errorMessage }}
            </div>
          </div>

          <!-- Custo da Tarefa -->
          <div class="form-group">
            <label for="cost">Custo (R$)</label>
            <input
              type="number"
              id="cost"
              v-model.number="cost"
              class="form-control"
              min="0"
              placeholder="Ex: 1000"
              required
            />
          </div>

          <!-- Data Limite da Tarefa -->
          <div class="form-group">
            <label for="dueDate">Data Limite</label>
            <input
              type="date"
              id="dueDate"
              v-model="dueDate"
              class="form-control"
              required
            />
          </div>

          <!-- Botões de Ação -->
          <div class="button-group">
            <button type="submit" class="btn btn-success">
              {{ taskData ? "Atualizar" : "Salvar" }}
            </button>
            <button type="button" class="btn btn-danger" @click="closeModal">
              Cancelar
            </button>
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "TaskModal",
  props: {
    taskData: { type: Object, default: null },
  },
  data() {
    return {
      name: "",
      cost: 0,
      dueDate: "",
      errorMessage: "",
    };
  },
  methods: {
    async onSubmit() {
      const task = { name: this.name, cost: this.cost, dueDate: this.dueDate };
      console.log("task:  ", task)
      try {
        if (this.taskData) {
          await axios.put(
            `${process.env.VUE_APP_API_URL}/task/update/${this.taskData.id}`,
            task,
          );
          this.$emit("task-updated");
        } else {
          await axios.post(`${process.env.VUE_APP_API_URL}/task/create`, task);
          this.$emit("task-created");
        }
        this.closeModal();
      } catch (error) {
        this.errorMessage = "Erro ao salvar a tarefa";
      }
    },
    closeModal() {
      this.$emit("close");
      this.resetForm();
    },
    resetForm() {
      this.name = "";
      this.cost = 0;
      this.dueDate = "";
      this.errorMessage = "";
    },
    formatDateForInput(date) {
      const formattedDate = new Date(date);
      const year = formattedDate.getFullYear();
      const month = String(formattedDate.getMonth() + 1).padStart(2, "0");
      const day = String(formattedDate.getDate()).padStart(2, "0");
      return `${year}-${month}-${day}`;
    },
  },
  mounted() {
    if (this.taskData) {
      this.name = this.taskData.name;
      this.cost = this.taskData.cost;
      this.dueDate = this.formatDateForInput(this.taskData.due_date);
      console.log("this.taskData.due_date: ", this.taskData.due_date);
      console.log("this.taskData.dueDate: ", this.taskData.dueDate);
    }
  },
};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  backdrop-filter: blur(5px);
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
  width: 400px;
  animation: fadeIn 0.3s;
}

.titulo {
  margin-bottom: 15px;
  text-align: center;
  color: #333;
  font-size: 1.5em;
}

.form-group {
  margin-bottom: 15px;
}

.form-control {
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1em;
}

.form-control:focus {
  border-color: #007bff;
  outline: none;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

.button-group {
  display: flex;
  justify-content: space-between;
}

.btn {
  padding: 10px 15px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1em;
}

.btn-success {
  background-color: #28a745;
  color: white;
}

.btn-danger {
  background-color: #dc3545;
  color: white;
}

.btn-success:hover {
  background-color: #218838;
}

.btn-danger:hover {
  background-color: #c82333;
}
.input-group {
  display: flex;
  align-items: center;
}

.input-group .btn {
  width: 40px;
  height: 100%;
}
@keyframes fadeIn {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
.autocomplete-list {
  list-style: none;
  padding: 0;
  margin: 0;
  border: 1px solid #ccc;
  max-height: 150px;
  overflow-y: auto;
}

.autocomplete-item {
  padding: 8px;
  cursor: pointer;
}

.autocomplete-item:hover {
  background-color: #f0f0f0;
}
.error-message {
  color: red;
  margin-top: 5px;
}

.upload-container {
  display: flex;
  flex-direction: column;
  align-items: center; /* Centraliza o conteúdo */
  border: 1px dashed #ccc; /* Borda pontilhada para o campo de upload */
  padding: 10px;
  border-radius: 8px; /* Arredondar cantos */
  background-color: #f9f9f9; /* Fundo leve */
  transition: border-color 0.3s ease; /* Transição suave ao focar */
}

.upload-container:hover {
  border-color: #007bff; /* Muda a cor da borda ao passar o mouse */
}

.form-control-file {
  margin-bottom: 10px; /* Espaço entre o campo de upload e a imagem */
  padding: 10px; /* Ajusta o preenchimento */
}

.icon-preview {
  margin-top: 10px; /* Espaçamento acima da imagem */
  border-radius: 8px; /* Arredondar cantos da imagem */
  overflow: hidden; /* Oculta qualquer parte da imagem que ultrapasse o contêiner */
}

.current-icon {
  max-width: 100px; /* Ajusta a largura máxima da imagem */
  max-height: 100px; /* Ajusta a altura máxima da imagem */
  border-radius: 8px; /* Arredondar cantos da imagem */
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2); /* Adiciona uma sombra sutil */
}
</style>
