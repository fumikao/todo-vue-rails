<template>
  <div>
    <div class="row">
      <div class="col s10 m11">
        <input v-model="newTask" class="form-control" placeholder="Add your task!!">
      </div>
      <div class="col s2 m1">
        <div @click="createTask" class="btn-floating waves-effect waves-light red">
          <i class="material-icons">add</i>
        </div>
      </div>
    </div>

    <div id="not-yet-tasks">
      <ul class="collection">
        <li :id="'row_task_' + task.id" class="collection-item" v-for="task in tasks" v-if="!task.is_done">
          <label>
            <input type="checkbox" @click="doneTask(task.id)" :id="'task_' + task.id" />
            <span class="black-text">{{ task.name }}</span>
            <button class="btn-floating btn-small waves-effect waves-light cyan lighten-3" @click="deleteTask(task.id)">
              <i class="material-icons">clear</i>
            </button>
          </label>
        </li>
      </ul>
    </div>

    <div class="btn" @click="displayFinishedTasks">Display finished tasks</div>

    <div id="finished-tasks" class="display_none">
      <ul class="collection">
        <li v-for="task in tasks" v-if="task.is_done" :id="'row_task_' + task.id" class="collection-item">
          <label>
            <input type="checkbox" @click="waitingTask(task.id)" :id="'task_' + task.id" checked="checked" />
            <span class="line-through">{{ task.name }}</span>
            <button class="btn-floating btn-small waves-effect waves-light cyan lighten-3" @click="deleteTask(task.id)">
              <i class="material-icons">clear</i>
            </button>
          </label>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  data(){
    return {
      tasks: [],
      newTask: ''
    }
  },
  mounted: function(){
    this.fetchTasks()
  },
  methods: {
    fetchTasks: function(){
      axios.get('/api/tasks').then((response) => {
        for(var i = 0; i < response.data.tasks.length; i++){
          this.tasks.push(response.data.tasks[i])
        }
      }, (error) => {
        console.log(error)
      })
    },
    displayFinishedTasks: function(){
      document.querySelector('#finished-tasks').classList.toggle('display_none')
    },
    createTask: function(){
      if (!this.newTask){
        alert('taskを入力してください')
        return
      };
      axios.post('/api/tasks', { task: { name: this.newTask } }).then((response) => {
        this.tasks.unshift(response.data.task)
        this.newTask = ''
      }, (error) => {
        console.log(error)
      })
    },
    deleteTask: function(task_id){
      var deleted_el = document.querySelector('#row_task_' + task_id)
      axios.delete('/api/tasks/' + task_id).then((response) => {
        deleted_el.classList.add('display_none')
      }, (error) => {
        console.log(error)
      })
    },
    doneTask: function(task_id){
      axios.put('/api/tasks/' + task_id, { task: { is_done: 1 } }).then((response) => {
        this.moveFinishedTask(task_id)
      }, (error) => {
        console.log(error)
      })
    },
    moveFinishedTask: function(task_id){
      var el = document.querySelector('#row_task_' + task_id)
      var el_clone = el.cloneNode(true)
      el.classList.add('display_none')
      el_clone.getElementsByTagName('input')[0].checked = 'checked'
      el_clone.getElementsByTagName('span')[0].classList.add('line-through')
      el_clone.getElementsByTagName('span')[0].classList.remove('black-text')
      var li = document.querySelector('#finished-tasks > ul > li:first-child')
      document.querySelector('#finished-tasks > ul').insertBefore(el_clone, li)
    },
    waitingTask: function(task_id){
      axios.put('/api/tasks/' + task_id, { task: { is_done: 0} }).then((response) => {
        this.moveNotYetTask(task_id)
      }, (error) => {
        console.log(error)
      })
    },
    moveNotYetTask: function(task_id){
      var el = document.querySelector('#row_task_' + task_id)
      var el_clone = el.cloneNode(true)
      el.classList.add('display_none')
      el_clone.getElementsByTagName('input')[0].checked = ''
      el_clone.getElementsByTagName('span')[0].classList.remove('line-through')
      el_clone.getElementsByTagName('span')[0].classList.add('black-text')
      var li = document.querySelector('#not-yet-tasks > ul > li:first-child')
      document.querySelector('#not-yet-tasks > ul').insertBefore(el_clone, li)
    }
  }
}
</script>

<style scoped>
[v-cloak] {
  display: none;
}

.display_none {
  display: none;
}

.line-through {
  text-decoration: line-through;
}

button {
  float: right;
}
</style>