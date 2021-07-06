<template>
  <div
    :class="[
      'main-wrapper',
      { 'body-dark': currentTheme === 'dark', 'body-light': currentTheme === 'light' },
    ]"
  >
    <div class="bg-img-container">
      <template v-if="currentTheme === 'dark'">
        <img v-if="windowWidth < 1100" class="bg-img" src="@/assets/bg-mobile-dark.jpg" />
        <img v-else class="bg-img" src="@/assets/bg-desktop-dark.jpg" />
      </template>
      <template v-else>
        <img v-if="windowWidth < 1100" class="bg-img" src="@/assets/bg-mobile-light.jpg" />
        <img v-else class="bg-img" src="@/assets/bg-desktop-light.jpg" />
      </template>
    </div>

    <div :class="['main-container', { 'margin-bottom': windowWidth > 1100 }]">
      <div class="title-icon-container">
        <h6 class="title">TODO</h6>
        <div style="cursor: pointer" @click="toggleCurrentTheme">
          <img v-if="currentTheme === 'dark'" src="@/assets/icon-sun.svg" alt="" />
          <img v-else src="@/assets/icon-moon.svg" alt="" />
        </div>
      </div>

      <div class="todo-container">
        <div
          :class="[
            'input-container',
            { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
          ]"
        >
          <span
            :class="[{ 'check-completed': inputs.inputChecked }, 'check-icon-container']"
            @click="inputs.inputChecked = !inputs.inputChecked"
          >
            <img
              v-if="inputs.inputChecked"
              class="check-icon"
              src="@/assets/icon-check.svg"
              alt=""
            />
          </span>
          <input
            :class="[
              'input-field',
              { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
            ]"
            placeholder="Create a new todo..."
            type="text"
            v-model="inputs.item"
            @keyup.enter="addItem"
          />
        </div>

        <div
          :class="[
            'display-todo-container',
            { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
          ]"
        >
          <draggable>
            <ul
              :class="[
                { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
              ]"
              v-for="(todo, index) in filteredTodoList"
              :key="index"
            >
              <li>
                <span
                  :class="[{ 'check-completed': todo.completed }, 'check-icon-container']"
                  @click="updateData(todo)"
                >
                  <img
                    v-if="todo.completed"
                    class="check-icon"
                    src="@/assets/icon-check.svg"
                    alt=""
                  />
                </span>
                <p :class="[{ 'item-completed': todo.completed === true }]">{{ todo.item }}</p>
                <img
                  class="cross-icon"
                  src="@/assets/icon-cross.svg"
                  alt=""
                  @click="deleteItem(todo, index)"
                />
              </li>
            </ul>
          </draggable>
        </div>

        <div
          :class="[
            'item-left',
            { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
          ]"
        >
          <span>{{ `${itemLeft} ${itemLeft === 1 ? 'item' : 'items'}` }} left</span>

          <div
            style="display: flex; justify-content: space-between; width: 150px; cursor: pointer"
            v-if="windowWidth > 1100"
          >
            <span
              :class="[{ 'active-filter': filterOption === 'all' }]"
              @click="filterOption = 'all'"
              >All
            </span>
            <span
              :class="[{ 'active-filter': filterOption === 'active' }]"
              @click="filterOption = 'active'"
              >Active</span
            >
            <span
              :class="[{ 'active-filter': filterOption === 'completed' }]"
              @click="filterOption = 'completed'"
              >Completed</span
            >
          </div>
          <span style="cursor: pointer" @click="deleteAllCompleted">Clear Completed</span>
        </div>

        <div
          :class="[
            'todo-filter',
            { 'dark-theme': currentTheme === 'dark', 'light-theme': currentTheme === 'light' },
          ]"
          v-if="windowWidth < 1100"
        >
          <span :class="[{ 'active-filter': filterOption === 'all' }]" @click="filterOption = 'all'"
            >All
          </span>
          <span
            :class="[{ 'active-filter': filterOption === 'active' }]"
            @click="filterOption = 'active'"
            >Active</span
          >
          <span
            :class="[{ 'active-filter': filterOption === 'completed' }]"
            @click="filterOption = 'completed'"
            >Completed</span
          >
        </div>
      </div>
      <div style="text-align: center; font-size: 1rem; color: #4d5066">
        <h6>Drag and drop to reorder list</h6>
      </div>
    </div>
  </div>
</template>

<script>
// import { todosCollection } from '../firebase';
// import { v4 as uuidv4 } from 'uuid';
import draggable from 'vuedraggable';
export default {
  components: {
    draggable,
  },
  data() {
    return {
      windowWidth: window.innerWidth,
      currentTheme: 'dark',
      filterOption: 'all',
      inputs: {
        item: '',
        inputChecked: false,
      },
      todoList: [],
    };
  },
  mounted() {
    // this.getData();
    window.addEventListener('resize', () => {
      this.windowWidth = window.innerWidth;
    });
  },
  methods: {
    /* -------------------------------------------------------------------------- */
    /*                              Firebase Methods                              */
    /* -------------------------------------------------------------------------- */
    // getData() {
    //   this.todoList = [];
    //   todosCollection.get().then(querySnapshot =>
    //     querySnapshot.forEach(doc => {
    //       const record = {
    //         id: doc.id,
    //         item: doc.data().item,
    //         completed: doc.data().completed,
    //       };

    //       this.todoList.push(record);
    //     }),
    //   );
    // },

    // postData(data) {
    //   todosCollection.doc(uuidv4()).set(data);
    // },

    // async updateData(record) {
    //   record.completed = !record.completed;
    //   await todosCollection.doc(record.id).update({ completed: record.completed });
    //   // await this.getData();
    // },
    // async deleteData(record) {
    //   await todosCollection.doc(record.id).delete();
    // },

    /* -------------------------------------------------------------------------- */
    /*                                    CRUD                                    */
    /* -------------------------------------------------------------------------- */
    async addItem() {
      const dt = { item: this.inputs.item, completed: this.inputs.inputChecked };
      this.todoList.push(dt);
      // await this.postData(dt);
      // await this.getData();
      if (this.inputs.item === '') return alert('Input field is empty');
      this.inputs.item = '';
    },

    async updateData(record) {
      record.completed = !record.completed;
      // await todosCollection.doc(record.id).update({ completed: record.completed });
      // await this.getData();
    },
    async deleteItem(dt, index) {
      // await this.deleteData(dt);
      this.todoList.splice(index, 1);
    },
    async deleteAllCompleted() {
      const newTodoList = [];

      this.todoList.forEach(async todo => {
        if (todo.completed === false) newTodoList.push(todo);
        if (todo.completed === true) {
          // await this.deleteData(todo).catch(console.error());
        }
      });

      this.todoList = newTodoList;

      // this.todoList = this.todoList.filter(el => el.completed === false);

      // const itemToBeDeleted = this.todoList.filter(el => el.completed === true);

      // itemToBeDeleted.forEach(async item => {
      //   await this.deleteData(item).catch(console.error());
      // });

      // const completedQuery = todosCollection.where('completed', '==', true);

      // completedQuery.get().then(async querySnapshot => {
      //   querySnapshot.docs.forEach(doc => {
      //     batch.delete(doc.ref);
      //   });

      //   batch.commit();

      //   return await this.getData();
      // });
    },
    toggleCurrentTheme() {
      this.currentTheme === 'dark' ? (this.currentTheme = 'light') : (this.currentTheme = 'dark');
    },
  },
  computed: {
    itemLeft() {
      const incompleteTodoList = this.todoList.filter(el => el.completed === false);
      return incompleteTodoList.length;
    },

    filteredTodoList() {
      if (this.filterOption === 'active') return this.todoList.filter(el => el.completed === false);
      if (this.filterOption === 'completed')
        return this.todoList.filter(el => el.completed === true);

      return this.todoList;
    },
  },
};
</script>

<style>
/* Light Theme

- Very Light Gray: hsl(0, 0%, 98%)
- Very Light Grayish Blue: hsl(236, 33%, 92%)
- Light Grayish Blue: hsl(233, 11%, 84%)
- Dark Grayish Blue: hsl(236, 9%, 61%)
- Very Dark Grayish Blue: hsl(235, 19%, 35%) */

/* .dark {
  color: hsl(235, 21%, 11%);
  color: hsl(235, 24%, 19%);
  color: hsl(234, 39%, 85%);
  color: hsl(236, 33%, 92%);
  color: hsl(234, 11%, 52%);
  color: hsl(233, 14%, 35%);
  color: hsl(237, 14%, 26%);
} */

/* Light Theme */

.body-light {
  background-color: #fafafa;
}

.light-theme {
  background-color: #ffffff;
  color: black;
}

/* .todo-container-light li,
.item-left-light,
.todo-filter-light {
  padding: 0 10px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  height: 45px;
  background-color: #ffffff;
  color: black;
  border-bottom: 1px solid #cacde8;
}  */

/* Dark Theme */

.body-dark {
  background-color: #161622;
}

.dark-theme {
  background-color: #25273c;
  color: #c9cbe2;
}

/* All CSS */

html {
  box-sizing: border-box;
  font-size: 100%;
  font-family: 'Josefin Sans', sans-serif;
}

*,
*::before,
*::after {
  box-sizing: inherit;
}
body {
  padding: 0;
  margin: 0;

  color: #c9cbe2;
  background-color: #161622;
  font-size: 2rem;
  font-weight: 400;
}

ul,
li {
  padding: 0;
  margin: 0;
  list-style-type: none;
}

img {
  object-fit: cover;
}

.main-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  height: 100%;
  z-index: -1;
}
.main-container {
  z-index: 2;
  max-width: 500px;
  width: 100%;
  height: 100%;
}

.bg-img-container {
  width: 100%;
}
.bg-img {
  top: 0;
  width: 100%;
  height: 40vh;
  position: absolute;
  z-index: 0;
}

.title-icon-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 20px;
  height: 90px;
  margin-top: 20px;
}

.title {
  color: #ffffff;
  font-size: 1.7rem;
  letter-spacing: 10px;
}

.todo-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 0 20px;
}

.input-container {
  display: flex;
  align-items: center;
  /* background-color: #25273c; */
  height: 50px;
  border-radius: 5px;
  margin-bottom: 10px;
  padding: 0 10px;
}
.input-field {
  margin: 0;
  padding: 0;
  width: 100%;
  /* background-color: #25273c; */
  /* color: #c9cbe2; */
  border: none;
  font-size: 0.65rem;
}

.input-field:focus {
  outline: none;
}
.todo-container ul {
  /* background-color: #25273c; */
  width: 100%;
}

/* .todo-container ul li:first-child {
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
} */

.display-todo-container {
  height: 280px;
  overflow: hidden;
  overflow-y: scroll;
}

.todo-container li,
.item-left,
.todo-filter {
  padding: 0 10px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
  height: 50px;
  /* background-color: #25273c; */
  border-bottom: 1px solid #4d5066;
}

.todo-container li {
  border-radius: none;
}

.item-left {
  /* border-top: 1px solid #cacde8; */
  font-size: 0.65rem;
  border-bottom: none;
  /* border-radius: 5px; */
  color: #4d5066;
  border-top: 1px solid #4d5066;
}

.todo-filter {
  margin-top: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 0.65rem;
  border-bottom: none;
  border-radius: 5px;
  color: #4d5066;
}
.todo-filter span {
  margin: 0 10px;
}

.todo-container p {
  width: 100%;
  text-align: left;
  font-size: 0.65rem;
}

.cross-icon,
.check-icon {
  width: 7px;
  height: 7px;
}

.check-icon-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 10px;
  min-height: 17px;
  height: 17px;
  min-width: 17px;
  width: 17px;
  padding: 5px;
  border-radius: 50%;
  border: 0.5px solid #4d5066;
}

.check-completed {
  background-image: linear-gradient(hsl(192, 100%, 67%), hsl(280, 87%, 65%));
  border: none;
}
.item-completed {
  text-decoration: line-through;
  color: #4d5066;
}

.active-filter {
  color: #5276c4;
}

@media screen and (min-device-width: 1100px) {
  .title-icon-container {
    margin-top: 70px;
  }
}

.display-todo-container::-webkit-scrollbar {
  display: none;
}

.margin-bottom {
  margin-bottom: 94px;
}
</style>
