<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-8 mt-5">
                <div class="card">
                    <div class="card-header">Create To Do task</div>
                    <div class="card-body">
                        <form @submit.prevent="saveTask()">
                          <input class="form-control form-control-lg"
                            :class="{'is-invalid':errors.name, 'is-valid': formData.name }"
                            type="text" v-model="formData.name"
                            placeholder="Enter a new task"
                            v-on:input="isValidData()"
                          />
                          <span class="text-danger" v-if="errors.name">
                            {{ errors.name }}
                          </span>
                        </form>
                        <div class="text-center mt-4"
                          v-if="tasksIsloading">
                          <img src="../../images/loader.gif" width="100px" />
                        </div>
                        <template v-else>
                          <ul class="list-group mt-4" v-if="tasks.length">
                            <li class="list-group-item" v-for="task in tasks"><b>{{ task.name }}</b></li>
                          </ul>
                          <p class="text-center mt-4" v-else>
                            <b>No task Found!</b>
                          </p>
                        </template>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    export default {
        data() {
          return {
            tasksIsloading: false,
            formData: {
              name: '',
            },
            tasks: [],
            errors: []
          };
        },
        created() {
          this.tasksIsloading = true;
          axios.get('tasks').then((resp) => {
            this.tasksIsloading = false;
            this.tasks = resp.data;
          }, (error) => {
            this.tasksIsloading = false;
            console.log(error)
          });
        },
        methods: {
          saveTask() {
            if (this.errors.length) return;
            const app = this;
            axios.post('tasks', app.formData).then((resp) => {
              app.formData = {};
              this.tasks.unshift(resp.data);
            }, (error) => {
              console.log(error)
            });
          },
          isValidData () {
            this.errors = [];
            if (!this.formData.name) {
              this.$set(this.errors, 'name', 'Name is required.');
            }
          }
        }
    }
</script>
