<template>
  <div id="app">
    <div v-if="isLoggedIn">
      <!-- We need some navigation links to switch between views -->
      <nav>
        <ul>
          <!--
            The RouterLink component comes with VueRouter.
            The 'to' prop is the URL route.
          -->
          <div class="router">
            <li><router-link to="/">All</router-link></li>
            <li><router-link to="/active">Active</router-link></li>
            <li><router-link to="/completed">Completed</router-link></li>
            <li class= "lougout"><button @click="logout">Logout</button></li>
          </div>
        </ul>
      </nav>
      <h1 class="title">Vue Todos</h1>
      <h4 class= "livedate"> 4/9/2018 </h4>
      <add-task-form @taskAdded="addTask" />
      <!--
        The RouterView is a placeholder that VueRouter uses to know
        where to insert the designated component for a given URL.
        In this case we are passing our taskList as a prop to each route's
        component and we are listening for user events.  This way we are still
        only mainting one master list in the App.vue component.
      -->
      <router-view
        :tasks="taskList"
        @toggleDone="toggleDone"
        @removeTask="removeTask"
      />
    </div>
    <login-page v-else @saveApiTokens="saveApiTokens" />
  </div>
</template>

<script>
/* eslint-disable */
import AddTaskForm from '@/components/AddTaskForm'
import LoginPage from '@/pages/LoginPage'
import axios from 'axios'
import moment from 'moment'
export default {
  name: 'App',
  components: { LoginPage, AddTaskForm },
  data () {
    return {
      api: {
        accessToken:null,
        expiresAt:null,
        },
      taskList: [
        // { id: 1234, title: 'Learn Vue', isComplete: true, priority: 'medium' },
        // { id: 1235, title: 'Learn Vue Router', isComplete: false, priority: 'medium' },
        // { id: 1236, title: 'Learn Vuex', isComplete: false, priority: 'medium' },
        // { id: 1237, title: 'Learn Vue DevTools', isComplete: true, priority: 'medium' }
      ]
    }
  },
  computed: {
    isLoggedIn () {
      return this.api.accessToken && moment(this.api.expiresAt).isAfter()
    }
  },
  created () {
    this.taskList = JSON.parse(localStorage.getItem('taskList')) || []
  },

  methods: {
    refreshTasks () {
      const axiosOptions = {
        baseURL: this.baseURL,
        headers: { 'Authorization': `Bearer ${this.api.accessToken}` }
      }
      axios
        .get('/todos', axiosOptions)
        .then(response => { this.taskList = response.data.data })
        .catch(error => { console.error(error) })
    },
    addTask (task) {
      this.taskList.push(task)
      this.saveTaskList()
    },

    toggleDone (task) {
      task.isComplete = !task.isComplete
      this.saveTaskList()
    },

    removeTask (task) {
      const taskIndex = this.taskList.indexOf(task)
      this.taskList.splice(taskIndex, 1)
      this.saveTaskList()
    },
    logout () {
    this.api.accessToken = null
    this.api.expiresAt = null
    localStorage.removeItem('todoApiTokens')
    },
    saveTaskList () {
      localStorage.setItem('taskList', JSON.stringify(this.taskList))
    },
    saveApiTokens (apiTokens) {
      this.api.accessToken = apiTokens.access_token
      this.api.expiresAt = apiTokens.expires_at
      localStorage.setItem('todoApiTokens', JSON.stringify(apiTokens))
      this.refreshTasks()
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  background-color: black;
  width: 100%;
  height: 60rem;
  max-width: 550px;
  margin: 60px auto;
  font-size: 20px;
  color: #808080;
  border-radius: 25px;
}
ul {
  list-style-type: none;
  margin: 10px;
  padding: 0;
  cursor: pointer;
}
nav ul {
  display: flex;
  justify-content: flex-start;
}
nav ul li {
  margin-right: 1em;
}
.router{
  position: absolute;
  margin-left: 148px;
  margin-top: 270px;
}
.livedate{
  margin-left: 218px;
  margin-top: -20px;
}
.lougout{
position: absolute;
margin-top: -220px;
margin-left: -50px;
}
</style>
