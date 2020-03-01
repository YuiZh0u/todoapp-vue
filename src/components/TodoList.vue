<template>
  <div>
    <input type="text"
    class="todo-input"
    placeholder="Lo que hay que hacer"
    v-model="newTodo"
    @keyup.enter="addTodo"
    >

    <todoitem v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index" class="todo-item" @Eliminado="removeTodo" @Editado="EditadoPadre" :checkAll="!anyRemaining">

    </todoitem>

    <div class="extra-container">
      <div>
        <label>
          <input type="checkbox" :checked="!anyRemaining" @change="checkAll"> Check All
        </label>
      </div>
      <div>
        {{remaining}} items restantes.
      </div>
    </div>

    <div class="extra-container">
      <div>
        <button :class="{active: filter == 'all'}" @click="filter = 'all'"> All </button>
        <button :class="{active: filter == 'active'}" @click="filter = 'active'"> Active </button>
        <button :class="{active: filter == 'completed'}" @click="filter = 'completed'"> Completos </button>

      </div>

      <div>
        <button v-if="showClearCompletedButton" @click="clearCompleted">
          Clear Completed
        </button>
      </div>

    </div>
  </div>
</template>

<script>
import todoitem from './TodoItem'

export default {
  name: 'todo-list',

  data (){
    return {
      newTodo: '',
      idForTodo: 3,
      beforeEditCache: '',
      filter: 'all',
      todos: [
        {
          'id': 1,
          'title': 'Primer item del todolist',
          'completed': false,
          'editing': false
        },
        {
          'id': 2,
          'title': 'Segundo item del todolist',
          'completed': false,
          'editing': false
        },
      ]
    }
  },

  computed: {
    remaining(){
      return this.todos.filter(item => !item.completed).length
    },
    anyRemaining(){
      return this.remaining != 0
    },
    todosFiltered(){
      if (this.filter == 'all') {
        return this.todos
      }else if (this.filter == 'active'){
        return this.todos.filter(item => !item.completed)
      }else if (this.filter == 'completed') {
        return this.todos.filter(item => item.completed)
      }

      return this.todos
    },
    showClearCompletedButton(){
      return this.todos.filter(item => item.completed).length > 0
    }
  },

  components: {
    todoitem,
  },

  methods: {

    addTodo(){
      if (this.newTodo.trim().length == 0) {
        return
      }
      this.todos.push({
        id: this.idForTodo,
        title: this.newTodo,
        completed: false
      })
      this.newTodo=''
      this.idForTodo++
    },
    removeTodo(index){
      this.todos.splice(index, 1);
    },
    // editTodo(todo){
    //   this.beforeEditCache = todo.title
    //   todo.editing = true
    // },
    // doneEdit(todo){
    //   if (todo.title.trim() == '') {
    //     todo.title = this.beforeEditCache
    //   }
    //   todo.editing = false
    // },
    // cancelEdit(todo){
    //   todo.title = this.beforeEditCache
    //   todo.editing = false
    // },
    checkAll(){
      this.todos.forEach((todo) => todo.completed = event.target.checked)
    },
    clearCompleted(){
      this.todos = this.todos.filter(item => !item.completed)
    },
    EditadoPadre(data){
      this.todos.splice(data.index, 1 , data.todo)
    }
  },

}
</script>

<style lang="scss">
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    &:focus {
      outline: 0;
    }
  }
    .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: 0.3s;
  }
  .remove-item {
    cursor: pointer;
    margin-left: 14px;
    &:hover {
      color: black;
    }
  }
  .todo-item-left { // later
    display: flex;
    align-items: center;
  }
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }
  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc; //override defaults
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    &:focus {
      outline: none;
    }
  }
  .completed {
    text-decoration: line-through;
    color: grey;
  }
  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 14px;
    margin-bottom: 14px;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    &:hover {
      background: lightseagreen;
    }
    &:focus {
      outline: none;
    }
  }
  .active {
    background: lightseagreen;
  }
  // CSS Transitions
  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>