<template>
  <div class="container">
    <div class="row">
      <div class="col-md-6">
        <div class="todolist not-done">
          <h1>Todos</h1>
          <v-row>
            <v-col cols="11">
              <v-text-field
                v-model="todo"
                label="Add Todo"
                required
              ></v-text-field>
            </v-col>

            <v-col cols="1" class="mt-7">
              <v-icon
                :disabled="todo.trim() == ''"
                color="black"
                @click="addTodo()"
              >
                mdi-plus-circle
              </v-icon>
            </v-col>
          </v-row>
          <v-btn block @click="allDone()" class="mb-3">
            Mark all as done
          </v-btn>

          <v-simple-table class="my-6 basil">
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-center font-weight-bold">
                    Task
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="t of todos" :key="t.id" @click="setDone(t)">
                  <td class="text-center" v-if="t.status == false">
                    {{ t.todo_name }}
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
          <div class="p-5 todo-footer min-vh-100">
            <strong
              ><span>{{
                todos.filter((el) => el.status == false).length
              }}</span></strong
            >
            Tasks Left
          </div>
        </div>
      </div>
      <div class="col-md-6">
        <div class="todolist">
          <h1 class=" mb-12">Done</h1>

          <hr />
          <v-btn block @click="deleteAll()" class="mt-5">
            Delete All
          </v-btn>
          <v-simple-table dark class="my-6">
            <template v-slot:default>
              <thead>
                <tr>
                  <th class="text-center">
                    TASK
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="t of todos" :key="t.id" @click="deleteTodo(t)">
                  <td v-if="t.status == true" class="text-center">
                    <strike> {{ t.todo_name }}</strike>
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
          <div class="p-5 todo-footer">
            <strong
              ><span>{{
                todos.filter((el) => el.status == true).length
              }}</span></strong
            >
            Tasks done
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  data() {
    return {
      todos: [],
      todo: '',

      valid: false,
    };
  },
  created() {
    this.getTodos();
  },
  methods: {
    async getTodos() {
      let { data } = await axios({
        url: '/todoList',
        method: 'GET',
      });

      this.todos = data;
    },
    async setDone(t) {
      await axios({
        url: '/todoList/' + t.id,
        method: 'PATCH',
        contentType: 'application/json',
        data: {
          status: true,
        },
      });
      this.getTodos();
    },
    async addTodo() {
      await axios({
        url: '/todoList',
        method: 'POST',
        contentType: 'application/json',
        data: {
          todo_name: this.todo,
          status: false,
        },
      });
      this.getTodos();

      this.todo = '';
    },
    async allDone() {
      for (
        let index = 0;
        index < this.todos.filter((el) => el.status == false).length;
        index++
      ) {
        await axios({
          url: '/todoList/' + this.todos[index].id,
          method: 'PATCH',
          contentType: 'application/json',
          data: {
            status: true,
          },
        });
      }

      this.getTodos();
    },

    async deleteTodo(t) {
      await axios({
        url: '/todoList/' + t.id,
        method: 'DELETE',
      });
      this.getTodos();
    },

    async deleteAll() {
      for (
        let index = 0;
        index < this.todos.filter((el) => el.status == true).length;
        index++
      ) {
        await axios({
          url: '/todoList/' + this.todos[index].id,
          method: 'DELETE',
        });
      }

      this.getTodos();
    },
  },
};
</script>

<style scoped>
h1{ 
  font-size: 5em;
}
* {
  font-family: 'Indie Flower', cursive;
  font-weight: bold;
}
@import url('https://fonts.googleapis.com/css2?family=Indie+Flower&display=swap');
.basil {
  background-color: #fffbe6 !important;
}
.basil--text {
  color: #356859 !important;
}
body {
  background-color: #eeeeee;
}
.todolist {
  background-color: #fff;
  padding: 20px 20px 10px 20px;
  margin-top: 30px;
}
.todolist h1 {
  margin: 0;
  padding-bottom: 20px;
  text-align: center;
}
.form-control {
  border-radius: 0;
}
li.ui-state-default {
  background: #fff;
  border: none;
  border-bottom: 1px solid #ddd;
}

li.ui-state-default:last-child {
  border-bottom: none;
}

.todo-footer {
  background-color: #f4fce8;
  margin: 0 -20px -10px -20px;
  padding: 10px 20px;
}
#done-items li {
  padding: 10px 0;
  border-bottom: 1px solid #ddd;
  text-decoration: line-through;
}
#done-items li:last-child {
  border-bottom: none;
}
#checkAll {
  margin-top: 10px;
}
</style>
