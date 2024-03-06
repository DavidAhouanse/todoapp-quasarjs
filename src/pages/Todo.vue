<template>
  <q-page class="bg-grey-3 column">
    <div class="row q-pa-sm bg-primary">
      <q-input
        v-model="newTask"
        @keyup.enter="createTask"
        class="col"
        square
        filled
        bg-color="white"
        placeholder="Add task"
        dense>

        <template v-slot:append>
          <q-btn
            @click="createTask"
            round
            dense
            flat
            icon="add" />
        </template>

      </q-input>
    </div>
    <q-list class="bg-white"
      separator
      bordered >

      <q-item
        v-for="(task, index) in tasks"
        :key="index"

        @click="task.done = !task.done"
        :class="{ 'done bg-blue-1' : task.done }"
        clickable
        v-ripple>
        <q-item-section avatar>
          <q-checkbox
            v-model="task.done"
            class="no-pointer-events"
            color="primary" />
        </q-item-section>

        <!-- <q-item-section v-if="!editingMode">
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>
        <q-input v-else
          v-model="taskToEdit"
          @keyup.enter="saveEditedTaskK"
          class="col"
          square
          filled
          bg-color="white"
          dense>

          <template v-slot:append>
            <q-btn
              @click="saveEditedTask"
              round
              dense
              flat
              icon="save" />
          </template>
        </q-input> -->

        <q-item-section >
          <q-item-label>{{ task.title }}</q-item-label>
        </q-item-section>

        <q-item-section
          side>
          <q-btn
            @click.stop="editTask(task)"
            flat
            round
            dense
            color="primary"
            icon="edit" />
        </q-item-section>

        <q-item-section
          side>
          <q-btn
            @click.stop="deleteTask(task.id)"
            flat
            round
            dense
            color="primary"
            icon="delete" />

        </q-item-section>

      </q-item>

    </q-list>

    <div
      v-if="!tasks.length"
      class="no-tasks absolute-center">
      <q-icon
        name="check"
        size="100px"
        coloir="primary">
      </q-icon>
      <div class="text-h5 text-primary text-center">
        No tasks
      </div>
    </div>
  </q-page>
</template>

<script>
import { api } from 'boot/axios'

export default {

  data(){
    return {
      newTask: "",
      taskToEditId:"",
      editingMode: false,
      editingTask: "",
      tasks: [
        // {
        //   title: 'Get bananas',
        //   done: false
        // },
        // {
        //   title: 'Eat bananas',
        //   done: false
        // },{
        //   title: 'Poo bananas',
        //   done: false
        // }
      ]
    }
  },

  methods:{
    /* deleteTask(index){
      this.$q.dialog({
        title: 'Confirm',
        message: 'Really want to delete ?',
        cancel: true,
        persistent: true
      }).onOk(() => {
        // console.log('>>>> OK')
        this.tasks.splice(index,1);
        this.$q.notify('Task deleted');

      })
    },

    addTask(){
      console.log("Add task");
      this.tasks.push({
        title : this.newTask,
        done: false,
      })
      this.newTask = "";
      this.$q.notify('Task added');
    } */

    /**
     * Mettre en majuscule le premier caractère
     */
     capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },



    /**
     * Gestion des erreurs
     */
    errorsHandling(error){
      if (error.response) {
        // Erreur de réponse du serveur (ex : 404, 500)
        console.error("Erreur de réponse du serveur :", error.response.status);
        console.error("Message d'erreur :", error.response.data);
      } else if (error.request) {
        // Aucune réponse du serveur (problème de réseau)
        console.error('Pas de réponse du serveur :', error.request);
      } else {
        // Erreur lors de la configuration de la requête
        console.error('Erreur lors de la configuration de la requête :', error.message);
      }
    },

    /**
     * Modifier une tâche
     */
    editTask(task){
      this.$q.dialog({
        //title: 'Prompt',
        message: 'Edit task',
        prompt: {
          model: task.title,
          type: 'text' // optional
        },
        cancel: true,
        persistent: true
      }).onOk((data) => {
        //console.log('Data ::', data);
        api
          .put("http://localhost:3050/tasks/" + task.id, {
            title: this.capitalizeFirstChar(data),
          })
          .then(response => {
            console.log(response.data)
            location.reload();
          })
          .catch(error => {
            // Gestion des erreurs
            this.errorsHandling(error);
          });

      })
    },

    /**
     * Ajouter une tâche
     */
    createTask() {
      //this.isLoading = true;
      if (this.newTask.length === 0) return;
      console.log(this.newTask)
      api
        .post("http://localhost:3050/tasks", {
          // Mettre la première lettre en majuscule et concaténer avec le reste de la chaîne
          title: this.capitalizeFirstChar(this.newTask),
          //state : this.newState,
        })
        .then(response => {
          this.newTask = "";
          //this.newState = "";
          this.message = response.data;
          //this.isLoading = true;
          location.reload();
          //console.log(this.message);
        })
        .catch(error => {
          // Gestion des erreurs
          //this.isLoading = true;
          this.errorsHandling(error);
        });
    },

    /**
     * Supprimer une tâche
     */
    deleteTask(taskId) {
      console.log(taskId);
      api
        .delete("http://localhost:3050/tasks/" + taskId)
        .then(response => {
          console.log(response.data)
          location.reload();
        })
        .catch(error => {
          // Gestion des erreurs
          this.errorsHandling(error);
        });
    },
  },

  created() {
    api
      .get("http://localhost:3050/tasks/")
      .then(response => (this.tasks = response.data))
      .catch(error => console.log(error));
  }

}
</script>

<style lang="scss">
.done {
  .q-item__label {
    text-decoration: line-through;
    color: #bbbb;
  }
}

.no-tasks {
  opacity: 0.5;
}
</style>
