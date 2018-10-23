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
    
    <div>
      Search: <input v-model="textFilter". list="texts">
      <datalist id="texts">
        <option v-for="task in tasks">{{ task.text }}</option>
      </datalist>
    </div>

    <div>
      <button @click="setSortAttribute('text')">Sort by Text</button>
    </div>


    <transition-group name="fade">
      <ul>
      <li v-for="task in tasks"  v-bind:key="task.id" v-bind:class="{strike: task.completed}" v-on:click="completeTask(task)">{{ task.text }}</li>
      </ul>
    </transition-group>

    
  </div>
</template>

<style>
.strike {
  text-decoration: line-through;
}

/* Vue.js fade */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s
}
.fade-enter, .fade-leave-to {
  opacity: 0
}

/* Vue.js slide-right */
.slide-right-enter-active {
  transition: all .3s ease;
}
.slide-right-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-right-enter, .slide-right-leave-to {
  transform: translateX(10px);
  opacity: 0;
}

/* Vue.js slide-left */
.slide-left-enter-active {
  transition: all .3s ease;
}
.slide-left-leave-active {
  transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
}
.slide-left-enter, .slide-left-leave-to {
  transform: translateX(-10px);
  opacity: 0;
}
</style>

<script>
var axios = require('axios')

export default {
  data: function() {
    return {
      tasks: [],
      newTask: {text: '', completed: false},
      textFilter: "",
      sortAttribute: 'text',
      sortOrder: 1
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
    },
    setSortAttribute: function(inputAttribute) {
      if (this.sortAttribute === inputAttribute) {
        this.sortOrder = this.sortOrder * -1;
      } else {
        this.sortOrder = 1;
      }
      this.sortAttribute = inputAttribute;
    }
  },
  computed: {}
};
</script>
