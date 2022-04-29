<template>
  <div class="container">
    <Header @toggle-add-task="toggleAddTask" title="Task Tracker" :showAddTask="showAddTask" />
    <div v-show="showAddTask">
    <AddTask @add-task="addTask" />
    </div>
    <Tasks 
    @toggle-reminder="toggleReminder"
   @delete-task="deleteTask" :tasks="tasks" />
  </div>
</template>

<script>
import Header from './components/Header'
import Tasks from './components/Tasks'
import AddTask from './components/AddTask'

export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    AddTask
  },
  data(){
    return{
      tasks: [],
      showAddTask: false
    }
  },
  methods: {
    toggleAddTask(){
      this.showAddTask = !this.showAddTask
    },
    async addTask(task){
      const res = await fetch('http://localhost:5000/tasks', {
        method: 'POST',
        headers: {
          'Content-type' : 'application/json',
        },
        body: JSON.stringify(task)
      }) 
      const data = await res.json()

      this.tasks = [...this.tasks, data]
    },
    deleteTask(id){
      if(confirm('Are you sure ?')){
        this.tasks = this.tasks.filter((task) => task.id !== id)
      }
    },
    toggleReminder(id){
      //console.log(id)
      this.tasks = this.tasks.map((task) => 
      task.id === id ? { ...task, reminder: !task.reminder } : task)
    },
      async fetchTasks(){
      const res = await fetch(`http://localhost:5000/tasks`)
      const data = await res.json()

      return data
      },
    // async fetchTask(id){
    //   const res = await fetch(`api/tasks/${id}`)
    //   const data = await res.json()

    //   return data
    //   },
    },
    async created(){
      this.tasks = await this.fetchTasks()
    },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
 }
  .add-form {
    margin-bottom: 40px;
  }
  .form-control {
    margin: 20px 0;
  }
  .form-control label {
    display: block;
  }
  .form-control input {
    width: 30%;
    height: 40px;
    background: rgb(235, 222, 222);
    margin: 5px;
    padding: 3px 7px;
    font-size: 17px;
  }
  .form-control-check {
    padding: 3px 7px;
    align-items: center;
    justify-content: space-between;
  }
  .form-control-check label {
    flex: 1;
  }
  .form-control-check input {
    flex: 2;
    height: 20px;
}
</style>
