<template>
  <div class="parent-div">
    <Todo-Header>
      <div class="flex">
        <input 
          type="text" 
          name="" 
          placeholder="New todo here . . ."
          class="my-input"
          v-model="new_todo_desc"
        >
        <button class="btn-add" @click="add_todo()" v-if="is_add">Add</button>
        <button class="btn-add" @click="update_todo" v-else>Update</button>
      </div>
      <p class="invalid-input" v-if="is_empty_field">Invalid value!</p>
    </Todo-Header>
    <Todo-Main>
      <p class="no-todos" v-if="todos.length < 1">No todos available!</p>
      <div class="todos-main-container" v-else>
        <div 
          class="todos-container" 
          v-for="(todo, index) in todos"
          :key="index"
        >
          <div class="todos-text-container">
            <p class="todo-date">{{todo.date}}</p>
            <p class="todo-desc">{{todo.desc}}</p>
          </div>
          <div class="common-btn-container">
            <button class="btn-common" @click="edit_todo(index, todo.date, todo.desc)"><svg class="common-btn-svg" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path></svg></button>
            <button class="btn-common" @click="delete_todo(index)"><svg class="common-btn-svg" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg></button>
          </div>
        </div>
      </div>
    </Todo-Main>
  </div>
</template>

<script>
  import TodoHeader from './components/TodoHeader.vue';
  import TodoMain from './components/TodoMain.vue';
  import {ref, reactive} from 'vue';
  import swal from 'sweetalert';
  export default { 
    components : {
      TodoHeader,
      TodoMain,
    },
    setup() {
      const edit_todo_index = ref(0);
      const edit_todo_date = ref('');
      const new_todo_desc = ref('');
      const is_empty_field = ref(false);
      const is_add = ref(true);
      const todos = reactive([]);
      const add_todo = () => {
        if (new_todo_desc.value === '') {
          is_empty_field.value = true;
        }
        else {
          is_empty_field.value = false;
          swal({
            text: "Todo item added successfully!",
            icon: "success",
            button: "Okay",
          });
          setTimeout(() => {
            todos.push({
              date : new Date().toDateString(),
              desc : new_todo_desc.value
            });
            new_todo_desc.value = '';
          }, 600)
        }
      }
      const edit_todo = (ind, date, desc) => {
        is_add.value = false;
        edit_todo_index.value = ind;
        edit_todo_date.value = date;
        new_todo_desc.value = desc;
      }
      const update_todo = () => {
        if (new_todo_desc.value === '') {
          is_empty_field.value = true;
        }
        else {
          swal({
            icon: "success",
            text: "Todo updated successfully!",
          });
          todos[edit_todo_index.value] = {
            date : edit_todo_date.value,
            desc : new_todo_desc.value,
          }
          is_add.value = true;
          new_todo_desc.value = '';
        }
      }
      const delete_todo = (index) => {
        swal({
          title: "Delete todo",
          text: "Are you sure on deleting this todo item?",
          icon: "warning",
          buttons: true,
          dangerMode: true,
        })
        .then((willDelete) => {
          if (willDelete) {
            todos.splice(index, 1);
            swal("Todo item has been deleted", {
              icon: "success",
            });
            new_todo_desc.value = '';
            is_empty_field.value = false;
          }
        });
      }
      return {
        todos,
        new_todo_desc,
        is_add,
        is_empty_field,
        add_todo,
        edit_todo,
        update_todo,
        delete_todo,
      }
    }
  }
</script>
