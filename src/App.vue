<template>
  <div @keydown.esc="offEdit()">
    <div class="todoInputsContainer">
      <div class="todoInputs">
        <md-field class="titleInput">
          <!-- Add to do -->
          <md-input
            style="text-align: center"
            v-model="currentTodo.title"
            type="text"
            placeholder="To do title"
          />
        </md-field>
        <md-field class="contentInput">
          <md-input
            style="text-align: center"
            v-model="currentTodo.content"
            @keydown.enter="addTodo()"
            type="text"
            placeholder="To do content"
          />
        </md-field>
      </div>
    </div>

    <!-- Show to do -->

    <draggable
      class="dragContainer md-layout md-gutter md-alignment-center"
      v-model="todos"
      @start="drag = true"
      @end="drag = false"
    >
      <md-card
        md-with-hover
        class="md-layout-item md-xlarge-size-20 md-large-size-25 md-medium-size-33 md-small-size-50 md-xsmall-size-70 todoCard"
        @dblclick="editTodo(todo)"
        v-for="todo in todos"
        :key="todo.id"
      >
        <md-card-header v-show="editedTodoId !== todo.id">
          <div class="md-title">
            {{ todo.title }}
          </div>
        </md-card-header>

        <md-card-content
          v-show="editedTodoId !== todo.id"
          :class="{ completed: todo.completed }"
          ><div>
            {{ todo.content }}
          </div>
        </md-card-content>

        <!-- Edit mode -->
        <md-field v-show="editedTodoId == todo.id" style="display: block">
          <div>Title</div>
          <md-input
            v-show="editedTodoId == todo.id"
            v-model="todo.title"
            @keydown.enter="offEdit()"
            type="text"
          />
        </md-field>
        <md-field v-show="editedTodoId == todo.id" style="display: block">
          <div>Content</div>
          <md-input
            v-show="editedTodoId == todo.id"
            v-model="todo.content"
            @keydown.enter="offEdit()"
            type="text"
          />
        </md-field>
        <!-- Buttons -->
        <md-card-actions>
          <span class="completeBtnWrap">
            <md-button
              class="completeBtn md-icon-button md-accent md-raised"
              v-if="todo.completed"
              @click="completeTodo(todo)"
            >
              <md-icon class="completeBtn"
                >thumb_up
                <md-tooltip class="compTooltip" md-direction="left"
                  >Completed!</md-tooltip
                >
              </md-icon>
            </md-button>
            <md-button
              class="completeBtn md-icon-button md-raised"
              v-if="!todo.completed"
              @click="completeTodo(todo)"
            >
              <md-icon class="completeBtn"> thumb_up </md-icon>
            </md-button>
          </span>

          <md-button class="md-raised deleteBtn" @click="removeTodo(todo)"
            >Delete</md-button
          >

          <md-button
            class="md-fab md-mini md-plain editBtn"
            @click="editTodo(todo)"
          >
            <md-icon>edit</md-icon>
          </md-button>

          <md-button
            class="md-fab md-mini md-primary doneBtn"
            v-show="editedTodoId == todo.id"
            @click="offEdit()"
          >
            <md-icon>done</md-icon>
          </md-button>
        </md-card-actions>
      </md-card>
    </draggable>
  </div>
</template>

<script>
import draggable from "vuedraggable";
export default {
  components: {
    draggable,
  },
  data() {
    return {
      todos: [],
      currentTodo: {},
      editedTodoId: null,
    };
  },
  mounted() {
    if (localStorage.todoList) {
      this.todos = JSON.parse(localStorage.todoList);
    }
  },
  methods: {
    addTodo() {
      this.todos.push({
        id: this.todos.length,
        title: this.currentTodo.title,
        content: this.currentTodo.content,
        completed: false,
      });
      this.savetodo();
      this.currentTodo = {};
    },
    savetodo() {
      if (!localStorage.todo) {
        localStorage.setItem("todoList", JSON.stringify(this.todos));
      }
    },
    removeTodo(todo) {
      var index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);
      this.savetodo();
    },
    editTodo(todo) {
      this.editedTodoId = todo.id;
      this.savetodo();
    },
    offEdit() {
      if (this.editedTodoId !== null) {
        this.editedTodoId = null;
      }
      this.savetodo();
    },
    completeTodo(todo) {
      if (todo.completed === null) {
        todo.completed = true;
        this.savetodo();
      } else {
        todo.completed = null;
        this.savetodo();
      }
    },
  },
};
</script>

<style scoped>
.todoCard {
  margin: 0.5rem 0.25rem;
}

.editBtn,
.doneBtn,
.deleteBtn,
.completeBtnWrap {
  margin: 0 0.3rem !important;
}
.compTooltip {
  font-size: 0.75rem;
}
.todoInputsContainer {
  display: flex;
  justify-content: center;
}
.todoInputs {
  width: 45%;
}
</style>
