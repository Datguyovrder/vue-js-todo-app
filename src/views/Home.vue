<template>
  <div class="home">
    <h1>Todo App</h1>
    <div>
      <h3>New Task</h3>
      <div>
        <input v-model="newTask.text">
        <button v-on:click="addTask()">Add Task</button>
      </div>
    </div>
    <div>
      <h5>{{ numberOfIncompleteTasks() }} tasks left</h5>
      <button @click="removeCompleted()">Clear Completed Tasks</button>
    </div>
    <ul>
      <li v-for="task in tasks" v-bind:class="{strike: task.completed}" v-on:click="completeTask(task)">{{ task.text }}</li>
    </ul>
  </div>
</template>

<style>
.strike {
  text-decoration: line-through;
}
</style>

<script>
var axios = require('axios')

export default {
  data: function() {
    return {
      tasks: [],
      newTask: {text: '', completed: false}
    };
  },
  created: function() {
    axios
    .get("http://localhost:3000/api/tasks")
    .then(function(response) {
      this.tasks = response.data
    }.bind(this))
  },
  methods: {
    addTask: function() {
      var params = {
                    text: this.newTask.text
                   };

      axios
      .post("http://localhost:3000/api/tasks",params)
      .then(function(response) {
        this.tasks.push(this.newTask);
      }.bind(this));
      
      this.newTask = {text: "", completed: false};
    },
    completeTask: function(inputTask) {
      inputTask.completed = !inputTask.completed;
    },
    numberOfIncompleteTasks: function() {
      var count = 0;
      this.tasks.forEach(function(task) {
        if (!task.completed) {
          count++;
        }
      });
      return count;
    },
    removeCompleted: function() {
      var incompleteTasks = [];
      for(var i = 0; i < this.tasks.length; i++) {
        var task = this.tasks[i];
        if (!task.completed) {
          incompleteTasks.push(task);
        }
      }
      this.tasks = incompleteTasks;
    }
  },
  computed: {}
};
</script>
