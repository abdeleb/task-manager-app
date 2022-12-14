<template>
  <Nav />
  <main>
    <div class="main-header">
      <div class="header-container">
        <img src="../assets/logo-app.png" alt="" />
        <h1>Welcome to Task Manager</h1>
        <h2>This tool will help you organize your tasks easily and simply.</h2>
        <NewTask @fetchTasks="readFromStore" />
        <ButtonRounded
          class="button-rounded"
          @filterModeEvent="filterMode = !filterMode"
        />
      </div>
    </div>
    <div v-if="!filterMode" class="main-task">
      <TaskItem
        v-for="(task, index) in taskArray"
        :key="index"
        :taskData="task"
        @fetchTasks="readFromStore"
      />
    </div>
    <div v-else class="main-task">
      <TaskItem
        v-for="(task, index) in doneTaskArray"
        :key="index"
        :taskData="task"
        @fetchTasks="readFromStore"
      />
    </div>
  </main>
  <Footer />
</template>

<script setup>
import SplashScreen from "@/components/SplashScreen.vue";
import Nav from "@/components/Nav.vue";
import Footer from "@/components/Footer.vue";
import ButtonRounded from "./ButtonRounded.vue";
import NewTask from "@/components/NewTask.vue";
import TaskItem from "@/components/TaskItem.vue";
import { useTaskStore } from "@/stores/task.js";
import { ref } from "vue";

const taskStore = useTaskStore();

let taskArray = ref([]);
let doneTaskArray = ref([]);

let filterMode = ref(false);

async function readFromStore() {
  taskArray.value = await taskStore.fetchTasks();
  doneTaskArray.value = taskArray.value.filter(
    (element) => element.is_complete === true
  );
}
readFromStore();
</script>

<style scoped>
main {
  background-color: var(--bg-secondary-color);
  width: 100%;
  position: relative;
}

.main-header {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  box-shadow: rgba(149, 157, 165, 0.2) 0px 8px 24px;
}

.header-container {
  margin-top: 16px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 80%;
}

.header-container img {
  width: 75px;
}

.header-container h1 {
  font-size: 2.6rem;
  font-weight: bold;
  color: rgb(89, 89, 89);
}

.header-container h2 {
  font-weight: 300;
}

.main-task {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  align-content: center;
}

@media screen and (max-width: 769px) {
  .header-container h1 {
    font-size: 1.6rem;
  }

  .header-container h2 {
    font-size: 0.8rem;
  }
}

@media screen and (max-width: 460px) {
  .header-container h1 {
    font-size: 1.2rem;
  }

  .header-container h2 {
    font-size: 0.6rem;
  }
}
.button-rounded {
  margin-bottom: 20px;
}
</style>

<!-- 
**Hints**
1. ref() is used here!
2. (NewTask, TaskItem, Footer, Nav) components are used here! 
3. Tasks are going to be contained in an array here!
4. An async function is needed to get all of the tasks stored within the supabase database, this async function's body will contain the tasks value which be use to store the fetchTasks method which lives inside the userTaskStore. This function needs to be called within the setUp script in order to run within the first instance of this component lifecycle.
5. NewTask component will receive a customEvent on this instance of the homeView that will fire the add-to-do function
6. add-to-do function will receive 2 params/arguments that will tak a taskTitle and a taskDescription and the body of this async function will call the taskStore that calls the addTask function from the store that pushes the info of the task to the backEnd. This is possible by passing the 2 param/arguments that will be passed by the user from the inputs within the NewTask Component. 
7. TaskItem component will loop through the tasks-array that will print an individual instance of an individual TaskItem component. TaskItem will receive 3 customEvents on this instance of the homeView. 1 customEvent for toggling the task to show either a text or an icon to display if the task is completed or not completed. 1 customEevent for removing/deleting the task out of the array. 1 customEvent for editing the task title and description. This function needs to call the function mentioned on hint4.
7.1-customEvent will fire an async function that will take in 1 param/argument. On the body of this function the param/argument will be used to define 2 constants. 1 of this constants will take care of setting the boolean value to the opposite of the value that checks wether this task is_complete. 1 of this constants will take of calling the id of this specific task in order to call the right id. 
7.2-customEvent will fire an asynf function that will take in 1 param/argument. This async function's body will be used to call the deleteTaskmethod which will take the param/argument's id in order to delete the task. This function needs to call the function mentioned on hint4. 
7.3-customEvent will fire an async function that will take in 1 param/argument. this async function's body will be used to take in 2 constants. 1 constant will take in the param/argument newValue. 1 constant will be used to get the param/argument oldValue id. These 2 constants will be sent to the backend via the useTaskStore which holds an editTask method. This function needs to call the function mentioned on hint4.
-->
