<template>
  <div class="container sm search-bar">
    <!-- Search Bar -->
    <div class="mb-3">
      <input v-model="searchQuery" type="text" class="form-control search-input" placeholder="Title, Description" />
    </div>
    <!-- View Task  -->
    <table class="table table-striped">
      <thead>
        <tr>
          <th @click="sortTask('title')">
            Title
            <span v-if="sortBy === 'title'">{{ sortOrder === 'asc' ? '↑' : '↓' }}</span>
          </th>
          <th>Description</th>
          <th @click="sortTask('status')">
            Status
            <span v-if="sortBy === 'status'">{{ sortOrder === 'asc' ? '↑' : '↓' }}</span>
          </th>
          <th>Edit</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="task in paginatedTasks" :key="task.id">
          <td>{{ task.title }}</td>
          <td>
            <div>
              <span>
                {{ task.showFullDescription || task.description.length <= 50 ? task.description :
                  task.description.substring(0, 50) }} </span>
                  <a v-if="task.description.length > 50" href="#" @click.prevent="toggleDescription(task)"
                    style="margin-left: 0.5rem; color: black; text-decoration: underline; cursor: pointer; ">
                    {{ task.showFullDescription ? 'Show Less' : '...' }}
                  </a>
            </div>
          </td>
          <td>
            <label class="switch">
              <input type="checkbox" :checked="task.status === 1 ? true : false" @change="toggleStatus(task)">
              <span class="slider round"></span>
            </label>
          </td>
          <td class="d-flex justify-content-center">
            <EditTask :task="task" :setReload="setReload" />
            <button @click="handleDelete(task)" class="btn btn-danger" style="margin-left: 0.25rem;">
              Delete
            </button>
          </td>

        </tr>
      </tbody>
    </table>
    <!-- Pagination Controls -->
    <nav aria-label="Task list pagination">
      <ul class="pagination justify-content-center">
        <li class="page-item" :class="{ disabled: currentPage === 1 }">
          <button class="page-link" @click="changePage(currentPage - 1)">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
              class="bi bi-chevron-left" viewBox="0 0 16 16">
              <path
                d="M11.854 1.646a.5.5 0 0 1 0 .708L5.707 8l6.147 5.646a.5.5 0 0 1-.708.708l-6.5-6a.5.5 0 0 1 0-.708l6.5-6a.5.5 0 0 1 .708 0z" />
            </svg>
          </button>
        </li>
        <li class="page-item" v-for="page in totalPagesArray" :key="page" :class="{ active: page === currentPage }">
          <button class="page-link" @click="changePage(page)">
            {{ page }}
          </button>
        </li>
        <li class="page-item" :class="{ disabled: currentPage === totalPages }">
          <button class="page-link" @click="changePage(currentPage + 1)">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
              class="bi bi-chevron-right" viewBox="0 0 16 16">
              <path
                d="M4.146 1.646a.5.5 0 0 1 .708 0L10.293 8l-5.439 5.646a.5.5 0 0 1-.708-.708L9.293 8 4.146 2.354a.5.5 0 0 1 0-.708z" />
            </svg>
          </button>
        </li>
      </ul>
    </nav>
  </div>
</template>

<script>
import '../style/TaskTable.css';
import EditTask from './modals/EditTask.vue';
import axios from 'axios';
export default {
  props: {
    tasks: {
      type: Array,
      required: true,
      default: () => [],
    },
    setReload: {
      type: Function,
      required: true,
    },
    handleDelete: {
      type: Function,
      required: true,
    },
  },
  components: {
    EditTask,
  },
  data() {
    return {
      currentPage: 1,
      tasksPerPage: 10, // Adjust the number of tasks per page as needed
      searchQuery: '',
      sortBy: '', // Keeps track of the current sort field
      sortOrder: 'asc'
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filterTasks.length / this.tasksPerPage);
    },
    totalPagesArray() {
      // Returns an array of page numbers to generate pagination buttons
      return Array.from({ length: this.totalPages }, (_, index) => index + 1);
    },
    filterTasks() {
      let tasks = this.tasks.filter((task) => {
        const query = this.searchQuery.toLowerCase();
        return (
          task.title.toLowerCase().includes(query) || task.description.toLowerCase().includes(query) | (query)
        );
      });
      // Sort tasks based on sort criteria
      if (this.sortBy) {
        tasks = tasks.sort((a, b) => {
          const aValue = a[this.sortBy];
          const bValue = b[this.sortBy];
          if (this.sortOrder === 'asc') {
            return aValue > bValue ? 1 : -1;
          } else {
            return aValue < bValue ? 1 : -1;
          }
        });
      }
      return tasks;
    },
    paginatedTasks() {
      const startIndex = (this.currentPage - 1) * this.tasksPerPage;
      const endIndex = startIndex + this.tasksPerPage;
      return this.filterTasks.slice(startIndex, endIndex);
    },
  },
  methods: {
    toggleStatus(task) {
      try {
        const token = JSON.parse(localStorage.getItem("userInfo") || "").token;
        // Toggle the status between 1 (done) and 0 (not done)
        task.status = task.status === 1 ? 0 : 1;
        axios.patch(
          `${process.env.VUE_APP_API_URL}/api/task/${task.id}`,
          {
            status: task.status
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        );
      } catch (err) {
        console.error(err);
      }
      // Persist the change with a reload or API call
      this.setReload();
    },
    changePage(page) {
      console.log(page)
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page;
      }
    },
    toggleDescription(task) {
      task.showFullDescription = !task.showFullDescription;
    },
    sortTask(field) {
      if(this.sortBy === field) {
        // Toggle sort order if alreayd sorting by this field
        this.sortOrder =  this.sortOrder === 'asc' ? 'desc':'asc';
      } else {
        // Set the sort fiedl and default to ascending order
        this.sortBy =  field;
        this.sorOrder = 'asc';
      }
    }
  },
};
</script>
