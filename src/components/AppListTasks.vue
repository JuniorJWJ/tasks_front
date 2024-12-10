<template>
  <div class="task-list-container">
    <h2>Lista de Tarefas</h2>
    <table class="task-table">
      <thead>
        <tr>
          <th>Nome da Tarefa</th>
          <th>Custo</th>
          <th>Data Limite</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(task, index) in tasks"
          :key="task.id"
          :class="{ 'highlight-cost': task.cost >= 1000 }"
        >
          <td>{{ task.name }}</td>
          <td>R$ {{ task.cost }}</td>
          <td>{{ formatDate(task.due_date) }}</td>
          <td>
            <button @click="$emit('edit-task', task)" title="Editar">
              <i class="fas fa-edit"></i>
            </button>
            <button @click="$emit('delete-task', task)" title="Excluir">
              <i class="fas fa-trash-alt"></i>
            </button>
            <button
              @click="moveTaskUp(index)"
              :disabled="index === 0"
              title="Subir"
            >
              <i class="fas fa-arrow-up"></i>
            </button>
            <button
              @click="moveTaskDown(index)"
              :disabled="index === tasks.length - 1"
              title="Descer"
            >
              <i class="fas fa-arrow-down"></i>
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AppListTasks",
  data() {
    return {
      tasks: [],
    };
  },
  async created() {
    await this.getTasks();
  },
  methods: {
    async getTasks() {
      try {
        const response = await axios.get(`${process.env.VUE_APP_API_URL}/task`);
        this.tasks = response.data.tasks;
      } catch (error) {
        console.error("Erro ao buscar tarefas:", error);
      }
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      return date.toLocaleDateString("pt-BR", {
        day: "2-digit",
        month: "2-digit",
        year: "numeric",
      });
    },
    async refreshTasks() {
      await this.getTasks();
    },
    async moveTaskUp(index) {
      if (index > 0) {
        console.log(`Movendo a tarefa para cima: ID ${this.tasks[index].id}`);

        [this.tasks[index - 1], this.tasks[index]] = [
          this.tasks[index],
          this.tasks[index - 1],
        ];

        await this.updateAllOrders();
      }
    },

    async moveTaskDown(index) {
      if (index < this.tasks.length - 1) {
        console.log(`Movendo a tarefa para baixo: ID ${this.tasks[index].id}`);

        [this.tasks[index + 1], this.tasks[index]] = [
          this.tasks[index],
          this.tasks[index + 1],
        ];

        await this.updateAllOrders();
      }
    },

    async updateAllOrders() {
      try {
        const tasksOrder = this.tasks.map((task, index) => ({
          id: task.id,
          order_field: index,
        }));

        console.log("taskOrder", tasksOrder);
        await axios.put(`${process.env.VUE_APP_API_URL}/task/update-order`, {
          tasksOrder,
        });
      } catch (error) {
        console.error("Erro ao atualizar a ordem das tarefas:", error);
      }
    },
  },
};
</script>

<style scoped>
.task-list-container {
  max-width: 800px;
  margin: 20px auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h2 {
  text-align: center;
  color: #333;
}

.task-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

.task-table th,
.task-table td {
  padding: 12px;
  text-align: left;
  border-bottom: 1px solid #ddd;
}

.task-table th {
  background-color: #0288d1;
  color: white;
}
.task-table th:first-child {
  border-top-left-radius: 8px; /* Arredonda a beirada superior esquerda */
}

.task-table th:last-child {
  border-top-right-radius: 8px; /* Arredonda a beirada superior direita */
}
.task-table tr:last-child td:first-child {
  border-bottom-left-radius: 8px; /* Arredonda a beirada inferior esquerda */
}

.task-table tr:last-child td:last-child {
  border-bottom-right-radius: 8px; /* Arredonda a beirada inferior direita */
}

.task-table td button {
  background: none;
  border: none;
  color: #0288d1;
  cursor: pointer;
  margin-right: 8px;
  font-size: 1.1em;
}

.task-table td button:disabled {
  color: #ccc;
  cursor: not-allowed;
}

.task-table tr:hover {
  background-color: #01579b;
  color: white;
}

.highlight-cost {
  background-color: rgb(193, 222, 228);
}
</style>
