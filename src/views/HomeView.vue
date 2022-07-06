<template>
  <div  class="home">
    <h1 class="title">ToDo App</h1>

    <hr>
    
  
    <div class="columns">
      <div class="column is-3 is-offset-2">
        <form class="box" v-on:submit.prevent="addTask">
          <h2 class="subtitle">Add task</h2>


          <div class="field">
            <label class="label">Description</label>
            <div class="control">
              <input class="input" type="text" v-model="description1"> 
            </div> 
          </div>

          <div class="field">
            <lable class="lable">Status</lable>
            <div class="control">
              <div class="select">
                <select v-model="status1" >
                  <option value="todo">To Do</option>
                  <option value="done">Done</option>
                </select>
              </div>
            </div>
          </div>

          <div class="field">
            <div class="control">
              <button class="button is-link">Submit</button>
            </div>
          </div>
        </form>
      </div>

      <div class="column is-3 is-offset-2">
        <form class="box" >
          <h2 class="subtitle">Update task</h2>

          <div class="field">
            <lable class="lable">Task</lable>
            <div class="control">
              <div class="select">
                <select v-model="id" >
                  <option v-for="task1 in atasks"  v-bind:key="task1.id" :value="task1.id">  {{ task1.description }}</option>
                  
                </select>
              </div>
            </div>
          </div>


          <div class="field">
            <label class="label">New Description</label>
            <div class="control">
              <input class="input" type="text" v-model="description"> 
            </div> 
          </div>

          
          

          <div class="field">
            <div class="buttons">
              <button class="button is-link" @click="updateTask()">Update</button>
              <button class="button is-danger" @click="deleteTask(id)">Delate</button>
            </div>
          </div>
        </form>
      </div>
    </div>



   
    <div class="columns">
      <div class="column is-6">
        <h2 class="subtitle">To Do</h2>

        <div class="todo">
          <div class="card" v-for="task in todotasks"  v-bind:key="task.id" >
            <div class="card-content">{{task.description}}</div>
            
            <footer class="card-footer">
              <a class="card-footer-item" @click="setStatus(task.id, 'done')">Done</a>
            </footer>
          </div>
        </div>
      </div>
      <div class="column is-6">
        <h2 class="subtitle">Done</h2>

        <div class="done">
          <div class="card" v-for="task in donetasks"  v-bind:key="task.id">
            <div class="card-content">{{task.description}}</div>

            <footer class="card-footer">
              <a class="card-footer-item" @click="setStatus(task.id, 'todo')">To Do</a>
            </footer>
          </div>
        </div>
      </div>

    </div>


   
  </div>
</template>


<script>
import axios from 'axios'
//import { computed } from '@vue/runtime-core'

export default {
  name: 'HomeView',
  data (){
    return { 
      tasks : [],
      description: '',
      description1: '',
      status1: 'todo'
  }
 
  },
  mounted () {
    this.getTasks()
  },
  methods: {
    getTasks() {
      axios({
        method: 'get',
        url: 'http://127.0.0.1:8000/tasks/',
        auth: {
          username: 'admin',
          password: 'safi31opmlm'
        }
      }).then(response => this.tasks = response.data)
    },
    addTask() {
      if (this.description1) {
        axios({
          method: 'post',
          url: 'http://127.0.0.1:8000/tasks/',
          data: {
            description: this.description1,
            status: this.status1
          },
          auth: {
          username: 'admin',
          password: 'safi31opmlm'
        }
        }).then((response) => {
          let newTask ={
            'id': response.data.id,
            'description': this.description1,
            'status': this.status1
          }
          this.tasks.push(newTask)

          this.description1 = ''
          this.status1 = 'todo'
        }).catch((error) => {
          console.log(error)
        })
      }

    },
    updateTask() {
      if (this.id) {   

          axios({
          method: 'put',
          url: 'http://127.0.0.1:8000/tasks/'+ this.id + '/',
          headers: {
          'Content-Type': 'application/json',
        },
          data: {
            status: 'todo',
            description: this.description,
            },
          auth: {
          username: 'admin',
          password: 'safi31opmlm'
        }
        }).then(() => {
          this.description = ''
          this.id = ''
          //this.getTasks()
          

        })
      }

    },
    deleteTask(task_id){
      axios.delete('http://127.0.0.1:8000/tasks/'+ task_id + '/')

    },
    setStatus(task_id, status) {
      const task= this.tasks.filter(task => task.id === task_id)[0]
      const description = task.description

      axios({
        method: 'put',
        url: 'http://127.0.0.1:8000/tasks/'+ task_id + '/',
        headers: {
          'Content-Type': 'application/json',
        },
        data: {
          status: status,
          description: description
        },
        auth: {
          username: 'admin',
          password: 'safi31opmlm'
        }

      }).then(() => {
        task.status = status
      })
    
    } 
  },

  computed: {
  todotasks: function () {
    return this.tasks.filter(i => i.status === 'todo')
  },
  donetasks: function () {
    return this.tasks.filter(i => i.status === 'done')
  },
  atasks: function () {
    return this.tasks.filter(i => i)
  },
} 
}
</script>

<style lang="scss">
.select,select {
  width: 100%;
}
.card {
  margin-bottom: 20px;
}
.done {
  opacity: 0.3;
}
</style>
