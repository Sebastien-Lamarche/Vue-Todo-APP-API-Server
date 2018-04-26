<template>
  <div>
    <h2 class="listtitle">{{listTitle}}</h2>
    <div class="priority">
      <label for="priority-filter">Show priority</label>
      <select name="priority-filter" id="priority-filter" v-model="selectedPriority">
        <option value="">all</option>
        <option
          v-for="option in priorityOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>
    <div class="category">
      <label for="category-filter">Show category</label>
      <select name="category-filter" id="category-filter" v-model="selectedCategory">
        <option value="">all</option>
        <option
          v-for="option in categoryOptions"
          :value="option"
          :key="option">{{option}}</option>
      </select>
    </div>
<h2 class="today">Today</h2>
    <ul class="task-list">
      <li v-for="task in filteredTasks" :key="task.id" class="task-item">
       <li v-for="task in sortedTasks" :key="task.id" class="task-item">
      <li v-for="task in filteredPriorityTasks" :key="task.id" class="task-item">
        <span class="far"
          :class="{
            'fa-circle': ! task.isComplete,
            'fa-check-circle': task.isComplete
          }"
          @click="toggleDone(task)"></span>
        <span>{{ task.title }}</span>
        <span>{{ task.priority }}</span>
        <span class="far fa-trash-alt" @click="removeTask(task)"></span>
      </li>
    </ul>
  </div>
</template>

<div class="">
<span>{{ task.dueDate }}</span>
</div>

<script>
export default {
  // We are not actually managing the task list in this component.
  // We are only displaying the list. The parent component (App.vue) is
  // passing down a reference to the list in the 'tasks' prop.
  props: ['tasks', 'listTitle'],
  data () {
    return {
      priorityOptions: ['low', 'medium', 'high'],
      categoryOptions: ['home', 'school', 'work'],
      selectedPriority: '',
      selectedCategory: '',
      sortAscending: true
    }
  },
  computed: {
    sortedTasks () {
    /* eslint-disable */
      return [...this.filteredTasks].sort((a, b) => {
        const dateOne = new Date(a.dueDate)
        const dateTwo = new Date(b.dueDate)
        return this.sortAscending ? (dateOne - dateTwo) : (dateTwo - dateOne)
      })
    }
  },
  methods: {
    // Since this component does not own the task list data, we need to
    // notify the parent component that the user wants to toggle the
    // completion status of the task.  We do that by emitting a custom event,
    // and passing up the affected task with the event. We will listen for
    // this event in the parent component and call the appropriate method to
    // make the change to the data.
    toggleDone (task) {
      this.$emit('toggleDone', task)
    },
    removeTask (task) {
      this.$emit('removeTask', task)
    }
  }
}
</script>

<style>
/* We will add styles here later. */
.title{
margin-left: 165px;
}
.task-item {
  display: grid;
  grid-template-columns: auto 1fr auto auto;
  grid-gap: 1rem;
  align-items: center;
 display: block;
  width: 400px;
  margin: 15px auto 0;
}
li{
  float: left;
}
.toolbar {
  margin-bottom: 1rem;
}
button {
  cursor: pointer;
  position: relative;
 -webkit-transition-duration: 0.4s;
    transition-duration: 0.4s;
    background-color: white;
    color: black;
    border: 1px solid black;
    margin-left: 367px;
    font-size: 15px;
}
button:hover{
  background-color: #f44336;
    color: white;
}
.listtitle{
  margin-right: 17px;
  text-align: center;
}
.priority{
position: absolute;
margin-left: 20px;
margin-top: 35px;
}
.category{
position: absolute;
margin-left: 300px;
margin-top: 35px;
}
.today{
  margin-left: 10px;
  margin-top: 130px;
  margin-bottom: -20px;
}
</style>
