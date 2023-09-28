<template>
  <div class="container">
    <label for="" class="error" v-if="error"> {{ error }} </label>
    <div class="form">


      <input type="text" class="input" v-model.trim="taskInput" @keydown.enter="handelAddTask" />
      <input type="submit" v-if="taskInput && !edit" @click="handelAddTask" class="add" value="Add Task"
        @keydown="handelAddTask" />
      <button v-if="taskInput && edit" @click="EditTask" class="add" @keydown="handelAddTask"> Edit</button>



    </div>
    <!-- v-for="(task, index) in tasks" :key="task" -->
    <div class="tasks">
      <Draggable v-model="tasks" tag="div">
        <template #item="{ element: task }">
          <div class="task">

            <p @click="handleTaskStatus(task)" :style="{ textDecoration: task.done ? 'line-through' : 'none' }">{{
              task.title }}
            </p>


            <img @click="onEditTask(task.title)" v-if="!task.done" class="edit" src="./assets/images/edit.png" alt="">
            <img @click.stop="onDeleteTask(task.title)" v-if="task.done" class="delete" src="./assets/images/delete.png"
              alt="">
            <!-- <button @click="onDeleteTask(index)" class="delete">Delete</button> -->
          </div>
        </template>
      </Draggable>

    </div>
  </div>
</template>

<script>
import Draggable from 'vuedraggable';
export default {
  data() {
    return {
      taskInput: '',
      tasks: [],
      find: false,
      error: '',
      edit: false,
      delete: true,
      isDone: false,
      taskIndex: 0,
      divTask: 0
    };
  },
  components: {
    Draggable,
  },
  mounted() {
    let data = window.localStorage.getItem("tasks");
    if (data) {
      this.tasks = JSON.parse(data);
    }
  },
  methods: {
    handleTaskStatus(task) {
      task.done = !task.done;
      this.addDataToLocalStorage(this.tasks);
    },
    handelAddTask() {
      this.tasks.forEach((task) => {
        if (task.title.match(this.taskInput) && this.taskInput !== '') {
          this.find = true;
        }
      });
      if (!this.find && this.taskInput !== '') {
        this.tasks.push({ title: this.taskInput, done: false });
        this.addDataToLocalStorage(this.tasks);
        this.taskInput = '';
        this.error = '';
        this.find = false;
      } else if (this.find && this.taskInput !== '') {
        this.error = 'this task added before';
        this.taskInput = '';
        this.find = false;
      }
      else {
        this.error = 'Enter a Task';
      }
      console.log(this.chickTask);
      console.log(this.tasks);
    },
    onEditTask(title) {
      this.tasks.forEach((task, index) => {
        if (task.title === title) {
          this.edit = true;
          this.taskInput = this.tasks[ index ].title;
          this.taskIndex = index;
        }
      });

    },
    EditTask() {
      this.tasks[ this.taskIndex ].title = this.taskInput;
      this.taskInput = "";
      this.edit = false;
    },
    onDeleteTask(title) {
      this.tasks = this.tasks.filter(item => item.title !== title);
      this.addDataToLocalStorage(this.tasks);
    },
    addDataToLocalStorage(arrayOfTasks) {
      window.localStorage.setItem("tasks", JSON.stringify(arrayOfTasks));
    }
  },
};
</script>



<style scoped>
.container {
  width: 500px;
  margin: 20px auto;
}

.edit {
  width: 30px;
  height: 30px;
}

.form {
  background-color: #333;
  border-radius: 6px;
  padding: 20px;
  display: flex;
  align-items: center;

}



.input {
  padding: 10px;
  border: 1px solid #666;
  border-radius: 6px;
  flex: 1;
  background-color: #666;
  color: white;

}

.input:focus {
  outline: none;
}

/* .delete {
  background-color: red;
  color: #fff;
  border: none;
  border-radius: 10px;
  padding: 5px 10px;
} */

.add {
  border: none;
  background-color: #0288d0;
  color: white;
  padding: 10px;
  border-radius: 6px;
  margin-left: 10px;
  cursor: pointer;
}

.tasks {
  background-color: #333;
  margin-top: 20px;
  border-radius: 6px;
  padding: 20px;
}

.tasks .task {
  background-color: #666;
  padding: 12px 12px 12px 50px;
  border-radius: 6px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  transition: 0.3s;
  cursor: pointer;
  border: 1px solid #888;
  color: white;
}

.tasks .task:not(:last-child) {
  margin-bottom: 15px;
}

.tasks .task:hover {
  background-color: #999;
}

.tasks .task p {
  width: calc(100% - 32px);
}

.tasks .task.done p {
  text-decoration: line-through;
  /* position: relative; */
}

/* .tasks .task.done::before {
  content: "";
  position: absolute;
  border-color: #fff;
  border-style: solid;
  border-width: 0 2px 2px 0;
  top: 10px;
  left: 16px;
  transform: rotate(45deg);
  height: 15px;
  width: 7px;
  /* position: absolute;
  width: calc(100% - 60px);
  height: 2px;
  background-color: black;
  left: 3px; */
/* } */

.delete {
  width: 25px;
  height: 25px;
  /* display: block; */
}

.error {
  display: block;
  margin-bottom: 13px;
  text-align: center;
  color: tomato;
  font-size: 16px;
}
</style>
