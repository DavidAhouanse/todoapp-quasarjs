<template>
  <q-layout view="lHh Lpr lFf">
    <q-header>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />
      </q-toolbar>
      <div class="q-px-lg q-pt-xl q-mb-md">
        <div class="text-h3">Todo</div>
        <div class="tex-subtitle1">{{ todaysDate }}</div>
      </div>

      <q-img src="../statics/mountains.jpg"
      class="header-image absolute-top"/>
    </q-header>

    <q-drawer
        v-model="leftDrawerOpen"
        show-if-above
        :width="250"
        :breakpoint="600"
      >
        <q-scroll-area style="height: calc(100% - 185px); margin-top: 185px; border-right: 1px solid #ddd">
          <q-list padding>
            <q-item
              to="/"
              exact
              clickable
              v-ripple>
              <q-item-section avatar>
                <q-icon name="list" />
              </q-item-section>

              <q-item-section>
                Todo
              </q-item-section>
            </q-item>

            <q-item
              to="/help"
              exact
              clickable
              v-ripple>
              <q-item-section avatar>
                <q-icon name="help" />
              </q-item-section>

              <q-item-section>
                Help
              </q-item-section>
            </q-item>

          </q-list>
        </q-scroll-area>

        <q-img class="absolute-top" src="https://cdn.quasar.dev/img/material.png" style="height: 185px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="https://cdn.quasar.dev/img/boy-avatar.png">
            </q-avatar>
            <div class="text-weight-bold">David Ahouanse</div>
            <div>@david.ah</div>
          </div>
        </q-img>
    </q-drawer>


    <q-page-container>
      <router-view v-slot="{ Component }">
        <keep-alive>
          <component :is="Component" />
        </keep-alive>
  </router-view >
    </q-page-container>
  </q-layout>
</template>

<script>

import { date } from 'quasar'

/* const linksList = [
  {
    title: 'Docs',
    caption: 'quasar.dev',
    icon: 'school',
    link: 'https://quasar.dev'
  },
  {
    title: 'Github',
    caption: 'github.com/quasarframework',
    icon: 'code',
    link: 'https://github.com/quasarframework'
  },
  {
    title: 'Discord Chat Channel',
    caption: 'chat.quasar.dev',
    icon: 'chat',
    link: 'https://chat.quasar.dev'
  },
  {
    title: 'Forum',
    caption: 'forum.quasar.dev',
    icon: 'record_voice_over',
    link: 'https://forum.quasar.dev'
  },
  {
    title: 'Twitter',
    caption: '@quasarframework',
    icon: 'rss_feed',
    link: 'https://twitter.quasar.dev'
  },
  {
    title: 'Facebook',
    caption: '@QuasarFramework',
    icon: 'public',
    link: 'https://facebook.quasar.dev'
  },
  {
    title: 'Quasar Awesome',
    caption: 'Community Quasar projects',
    icon: 'favorite',
    link: 'https://awesome.quasar.dev'
  }
] */

export default {
  name: 'MainLayout',

  data() {
    return {
      leftDrawerOpen: false
    }
  },

  methods: {
    toggleLeftDrawer () {
      this.leftDrawerOpen = !this.leftDrawerOpen;
    },

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
     * Ajouter une tâche
     */
    createTask() {
      this.isLoading = true;

      if (this.newTitle.length === 0 && this.newState.length === 0) return;

      axios
        .post("http://localhost:3030/tasks", {
          // Mettre la première lettre en majuscule et concaténer avec le reste de la chaîne
          title: this.capitalizeFirstChar(this.newTitle),
          state : this.newState,
        })
        .then(response => {
          this.newTitle = "";
          this.newState = "";
          this.message = response.data;
          this.isLoading = true;
          location.reload();
          //console.log(this.message);
        })
        .catch(error => {
          // Gestion des erreurs
          this.isLoading = true;
          this.errorsHandling(error);
        });
    },

    /**
     * Supprimer une tâche
     */
    deleteTask(taskId) {
      console.log(taskId);
      axios
        .delete("http://localhost:3030/tasks/" + taskId)
        .then(response => {
          console.log(response.data)
          location.reload();
        })
        .catch(error => {
          // Gestion des erreurs
          this.errorsHandling(error);
        });
    },

    // activateEdit(task){
    //   //console.log(typeof task)
    //   this.newTitle = task.title;
    //   console.log(task.state);
    //   this.newState = task.state;
    //   this.toAddAction = false;
    //   this.idTaskToUpdate = task.id
    // },

    /**
     * Modifier une tâche
     */
    /* updateTask(){
      axios
        .put("http://localhost:3030/tasks/" + this.idTaskToUpdate, {
          title: this.capitalizeFirstChar(this.newTitle),
          state: this.newState
        })
        .then(response => {
          console.log(response.data)
          location.reload();
        })
        .catch(error => {
          // Gestion des erreurs
          this.errorsHandling(error);
        });
    }, */
  },

  computed: {
    todaysDate() {
      const timeStamp = Date.now()
      return date.formatDate(timeStamp, 'dddd D MMMM YYYY')
    }
  }
}
</script>

<style lang="scss">

.header-image {
  height: 100%;
  z-index: -1;
  opacity: 0.2;
  filter: grayscale(100%);

}
</style>
