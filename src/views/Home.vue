<template>
    <div v-if="showAddTask">  
      <AddTasks @add-task = "addTask" />
    </div>
    <Tasks @toggle-reminder="toggleReminder" @delete-task="deleteTask" :tasks="tasks" />
</template>

<script>
import Tasks from "../components/tasks.vue"
import AddTasks from "../components/AddTask.vue"

export default {
    name:'Home',
    props:{
        showAddTask: Boolean
    },
    components: {
        Tasks,
        AddTasks
    },

    data (){
        return{
            tasks: [],
        }
    },

    methods: {

    async addTask(task){
      const res = await fetch('api/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(task)
      })
      const data = await res.json()
      this.tasks = [...this.tasks, data]
    },

    async deleteTask(id){
      const res = await fetch(`api/tasks/${id}`,{
        method: 'DELETE'
      })
      res.status === 200 ? (this.tasks = this.tasks.filter((task) =>
        id !== task.id)) : alert('Error Deleting Task')
    },


    async toggleReminder(id){

      const taskToToggle = await this.fetchTask(id)
      const updateTask = {...taskToToggle, reminder:
      !taskToToggle.reminder}

      const res = await fetch(`api/tasks/${id}`,{
        method: 'PUT',
        headers: {
          'Content-type': 'application/json'
        },
        body: JSON.stringify(updateTask)
      })
      const data = await res.json()
      this.tasks = this.tasks.map((task) => task.id === id ? 
        {...task, reminder: data.reminder} : task)
    },

    async fetchTasks(){
      const res = await fetch('api/tasks');
      const data = await res.json();
      return data
    },

     async fetchTask(id){
      const res = await fetch(`api/tasks/${id}`);
      const data = await res.json();
      return data
    }
  },


  async created(){
    this.tasks = await this.fetchTasks()
  },

  emits: ['btn-click']
}

</script>
