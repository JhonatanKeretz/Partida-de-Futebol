<template>
  <div class="container mt-2">

    <b-form inline class="mb-2">
      <b-form-input 
      v-model="filter.subject"
      id="subject"
      placeholder="Ex: Partida Quinta"
      class="mr-2"
      autocomplete="off"
      >
      </b-form-input>

      <b-form-select
      v-model="filter.status"
      :options="optionsList"
      class="mr-2"
      >
      </b-form-select>
      <b-button
      variant="outline-secondary"
      @click="filterTasks"
      class="mr-2"
      v-b-tooltip.hover
      title="Buscar"
      ><i class="fa-solid fa-search"></i>
      </b-button>

       <b-button
      variant="outline-secondary"
      @click="clearFilter"
      class="mr-2"
      v-b-tooltip.hover
      title="Limpar Filtro"
      ><i class="fa-solid fa-times"></i>
      </b-button>

    </b-form>

    <template v-if="isLoading">
    <div class="loading-spin">
      <b-spinner style="width: 5rem; height: 5rem;"></b-spinner>
    </div>
    </template>

    <template v-if="isTasksEmpty && !isLoading">
     <div class="empty-data mt-2">
       <img src="../assets/images/empty-data.svg" class="empty-data-image">
       <b-button 
       variant="outline-primary" class="mt-2" size="lg" to="/form"
       >Criar Tarefa</b-button>
     </div>
     
    </template>
    <template v-if="!isTasksEmpty && !isLoading">
      <div v-for="(task) in tasks" :key="task.id">
        <b-card class="mb-2" :class="{ 'finished-task': isFinished(task) }">
        <div class="d-flex justify-content-between">
          <b-card-title>
          {{ task.subject }}
          </b-card-title>

          <span>
            <b-badge :variant="variantOverdue(task.dateOverdue, task.status)"
            >{{ overduePresente (task.dateOverdue) }}</b-badge>
          </span>
        </div>
        
          <b-card-text>{{ task.description }}</b-card-text>
         
          <b-button
            variant="outline-secondary"
            class="mr-2"
            @click="updateStatus(task.id, status.FINISHED)"
            v-b-tooltip.hover
            title="Concluir"
          >
            <i class="fa-solid fa-check"></i>
          </b-button> 

          <b-button
            variant="outline-secondary"
            class="mr-2"
            @click="updateStatus(task.id, status.ARCHIVED)"
            v-b-tooltip.hover
            title="Arquivar"
          >
           <i class="fa-solid fa-archive"></i>
          </b-button>
          <b-button
            variant="outline-secondary"
            class="mr-2"
            @click="edit(task.id)"
            v-b-tooltip.hover
            title="Editar"
          >
            <i class="fa-solid fa-edit"></i>
          </b-button>
          <b-button
            variant="outline-danger"
            class="mr-2"
            @click="remove(task.id)"
            v-b-tooltip.hover
            title="Apagar"
          >
            <i class="fa-solid fa-times"></i>
          </b-button>
        </b-card>
      </div>
    </template>

    <b-modal ref="modalRemove" hide-footer title="Exclusão de partida">
      <div class="d-block text-center">
        Deseja realmente excluir essa partida? {{ taskSelected.subject }}
      </div>
      <div class="mt-3 d-flex justify-content-end">
        <b-button variant="outline-secondary" class="mr-2" @click="hideModal">
          Cancelar
        </b-button>
        <b-button
          variant="outline-danger"
          class="mr-2"
          @click="confirmRemoveTask"
        >
          Excluir
        </b-button>
      </div>
    </b-modal>
  </div>
</template>

<script>
import TasksModel from "@/models/TasksModel";
import Status from "@/valueObjects/status";
import ToastMixin from "@/mixins/toastMixin.js";

export default {
  name: "List",

  mixins: [ToastMixin],

  data() {
    return {
      tasks: [],
      taskSelected: [],
      status: Status,
      filter: {
        subject: null,
        status: null
      },
     optionsList: [
        { value: null, text: "Selecione algum Status" }, 
        { value: Status.OPEN, text: "Partida Aberta" }, 
        { value: Status.FINISHED, text: "Partida Concluída" }, 
        { value: Status.ARCHIVED, text: "Partida Finalizada" }, 
      ],
      isLoading: false,
      statusList: [Status.OPEN, Status.FINISHED]
    };
  },

  async created() {
    //this.isLoading = true;
    this.tasks = await this.getTasks();
    //let self = this;
    //await setTimeout(function(){
    //  self.isLoading = false;
   // }, 3000)
    //this.tasks = await TasksModel.params({ 
      //  status: [ this.status.OPEN, this.status.FINISHED, ] 
        //}).get();
   //this.isLoading = false;
  },

  methods: {
    edit(taskId) {
      this.$router.push({ name: "form", params: { taskId } });
    },

    async remove(taskId) {
      this.taskSelected = await TasksModel.find(taskId);
      this.$refs.modalRemove.show();
    },

    hideModal() {
      this.$refs.modalRemove.hide();
    },

    async confirmRemoveTask() {
      this.taskSelected.delete();
      this.tasks = await this.getTasks();
     // this.tasks = await TasksModel.params({ 
       // status: [ this.status.OPEN, this.status.FINISHED, ] 
       // }).get();
      
      this.hideModal();
    },
    async updateStatus(taskId, status){
      let task  = await TasksModel.find(taskId);
      task.status = status;
      await task.save();

      this.tasks = await this.getTasks();

      //this.tasks = await TasksModel.params({ 
        //status: [ this.status.OPEN, this.status.FINISHED, ] 
       // }).get();

      this.showToast("success", "Sucesso!", "Status da partida atualizada com sucesso");
    },
    isFinished(task){
      return task.status === this.status.FINISHED;
      //if(task.status === this.status.FINISHED) {
      //  return true;
     // }
    },
    async filterTasks(){
      let filter = { ... this.filter };
      filter = this.clean(filter);
      filter.userId = JSON.parse(localStorage.getItem('authUser')).id;
       this.tasks = await TasksModel.params(filter).get();
    },
    clean(obj){
      for(var propName in obj){
        if(obj[propName] === null || obj[propName] === undefined ){
          delete obj[propName];
        }
      }
      return obj;
    },
    async clearFilter(){
      this.filter = {
        subject: null,
        status: null,
      };
      this.tasks = await this.getTasks();
      // this.tasks = await TasksModel.params({ 
        //status: [ this.status.OPEN, this.status.FINISHED, ] 
       // }).get();
    },
    overduePresente(dateOverdue){
      if(!dateOverdue){
        return;
      }
      // 2022-05-23 vai se tornar um array [2022, 08, 26]
      return dateOverdue.split('-').reverse().join('/');
    },
    variantOverdue(dateOverdue, taskStatus){
      if(!dateOverdue){
        return 'light';
      }
      if(taskStatus === this.status.FINISHED) {
        return 'success';
      }
      //2022-05-24
      let dateNow = new Date().toISOString().split("T")[0];
      if(dateOverdue === dateNow){
        return 'warning';
      }
      if(dateOverdue < dateNow){
        return 'danger';
      }
      return 'light'
    },
    async getTasks(){
      this.isLoading = true;
      let self = this;
      setTimeout(function(){ self.isLoading = false}, 1000);
      return await TasksModel.params({userId: JSON.parse(localStorage.getItem('authUser')).id,
      status: this.statusList
      })
      .get();
   }
  },
  computed: {
    isTasksEmpty() {
     return this.tasks.length === 0;
    },
  },
};
</script>

<style scoped>

  .empty-data {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
  }

  .empty-data-image {
    width: 300px;
    height: 300px;
  }

  .finished-task{
    opacity: 0.7;
  }

  .finished-task > .card-body > h4, .finished-task > .card-body > p {
    text-decoration: line-through;
  }

  .loading-spin {
    display: flex;
    align-items: center;
    justify-content: center;
    height: 65vh;
  }

</style>
