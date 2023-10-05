<template>
    <div class="wrapper d-flex flex-column align-items-center w-100">
        <h1 class="mb-5 h1 text-center">Todo Application</h1>
        <form class="d-flex justify-content-center w-50 mb-3">
            <input v-model="searchQuery" class="query-input form-control mr-2" name="user_search_query" type="search" placeholder="Search your task here..." aria-label="Search your task here">
        </form>
        <form @submit.prevent="handleData" class="d-flex justify-content-center w-50 mb-5">
            <input v-model="formData.task" class="task-input form-control mr-2" name="user_task" type="text" placeholder="Add new task here..." aria-label="Add new task">
            <button class="btn btn-success px-4" type="submit">Add</button>
        </form>
        <div class="w-50">
            <h2 class="text-center mb-4">My tasks</h2>
            <ul class="list-group list-group-flush">
       
            <div v-if="tasks.length == 0 || filteredTasks?.length == 0" class="w-100 border p-4 rounded text-center">
                {{ filteredTasks?.length == 0 ? "Not found !" : "Task box is empty !" }}
            </div>
      
            <template v-for="task in filteredTasks == null ? tasks : filteredTasks" :key="task">
                <li class="list-group-item bg-light" data-id="{{task.id}}">
                    <form class="d-flex align-items-center justify-content-center">
                        <input v-model="task.status" class="input-check" type="checkbox" :checked="task.status == true">
                    </form>
                      <p class="text text-muted m-0 p-0 task-title" :class="task.status && 'done'">{{task.task}}</p>
                    <div class="d-flex ml-auto">
                        <button class="btn btn-primary mr-1" @click="editTask(task.id)">
                            <img width="20" height="20" src="../src/assets/edit.svg" alt="close icon">
                        </button>

                        <button class="btn btn-danger" @click="deleteTask(task.id)">
                            <img width="20" height="20" src="../src/assets/delete.svg" alt="close icon">
                        </button>
                    </div>
                 </li>
            </template>
        </ul>
        </div>
    </div>
</template>

<script>
 export default {
   data() {
    return {
        formData: {
        task: ""
      },
      tasks: sessionStorage.getItem("tasks") ? JSON.parse(sessionStorage.getItem("tasks")) : [],
      searchQuery: sessionStorage.getItem("searchQuery") || "",
      filteredTasks: JSON.parse(sessionStorage.getItem("filteredTasks")) || null

    }
   },
   methods: {
    handleData(event) {
        const inp = document.querySelector('.task-input');
        if(this.formData.task == "") {
            inp.classList.add('is-invalid');
        }else {
            inp.classList.remove('is-invalid');
            this.tasks.push({
                id: this.tasks.length == 0 ? 1 : this.tasks.length + 1,
                task: inp.value,
                status: false
            });
            
            this.updateTasks();
            this.formData.task = "";
        }
    },
    deleteTask(id) {
        this.tasks = this.tasks.filter(item => item.id !== id);
        this.updateTasks();
    },
    editTask(id) {
        const foundItem = this.tasks.find(item => item.id === id);
        const foundItemIndex = this.tasks.findIndex(item => item.id === id);
        foundItem.task = prompt("Change task name: ", foundItem.task).trim();
        this.tasks[foundItemIndex] = foundItem;
        this.updateTasks();

    },
    updateTasks() {
        sessionStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    getStoredTasks() {
        return JSON.parse(sessionStorage.getItem("tasks") || []);
    },
    getSearchQuery() {
        return sessionStorage.getItem("searchQuery") || "";
    }

   },
   computed: {},
   watch: {
      tasks: {
        handler() {
            this.updateTasks();
        },
        deep: true
      },
      searchQuery: {
        handler(newValue) {
          if(newValue != '') {
            this.filteredTasks = this.tasks.filter(item => item.task.toLowerCase().startsWith(newValue));
            sessionStorage.setItem("filteredTasks", JSON.stringify(this.filteredTasks));
            sessionStorage.setItem("searchQuery", newValue);
          }else {
            this.filteredTasks = null;
            sessionStorage.setItem("searchQuery", "");
          }
        },
        // immediate: true
      }
   }
 }
</script>

<style scoped>
.btn {
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 14px;
}

.list-group-item {
    display: flex;
    align-items: center;
    gap: 10px;
}

.input-check {
    width: 20px;
    height: 20px;
}

.task-title {
    font-size: 16px;
}

.done {
    display: flex;
    align-items: center;
    gap: 10px;
    opacity: 0.8;
}

.done::after {
    text-decoration: none;
    color: green;
    content: "Done ✅";
}

</style>

