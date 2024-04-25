<template>
  <header>
    <div class="title"><h1>Task-V </h1></div>
    <div class="add-list">
      <input type="text" v-model="newListTitle" @keyup.enter="addList" placeholder="Add new list">
      <button @click="addList" class="add-button"id="addList">Add List</button>
   
    <button @click="deleteAllLists" id="buttondeleteallLists">Delete All Lists</button> </div>
  </header>
  <body>
    
 
  <div class="desktop">
    <div class="lists-container">
      <div v-for="(list, listIndex) in lists" :key="listIndex" class="list" ref="lists" @drop="dropList(listIndex)" @dragover.prevent>
        <div class="list-header">
          <h2>{{ list.title }}</h2>
          <button @click="deleteList(listIndex)" id="buttondeleteList">Delete List</button>
        </div>
        <div class="cards-container" ref="cards">
          <div v-for="(card, cardIndex) in list.cards" :key="cardIndex" class="card" draggable="true" @dragstart="drag(card, listIndex, cardIndex)">
            <div class="card-content">
              <span>{{ card.text }}</span>
              <button @click="deleteCard(listIndex, cardIndex)" id="buttondeleteCard">Delete</button>
            </div>
          </div>
        </div>
        <div class="add-card">
          <input type="text" v-model="newCardText[listIndex]" @keyup.enter="addCard(listIndex)" placeholder="Enter task" maxlength="15">
          <button @click="addCard(listIndex)" class="add-button" id="addTask">Add Task</button>
        </div>
      </div>
    </div>
  </div> </body>
</template>

<script>
export default {
  name: 'Desktop',
  data() {
    return {
      // Obtener las listas guardadas en localStorage, si las hay, de lo contrario, usar las listas predeterminadas
      lists: JSON.parse(localStorage.getItem('taskLists')) || [
        { id: 1, title: 'To Do', cards: [{ id: 1, text: 'Task 1' }, { id: 2, text: 'Task 2' }] },
        { id: 2, title: 'In Progress', cards: [{ id: 3, text: 'Task 3' },{ id: 4, text: 'Task 4' }] },
        { id: 3, title: 'Done', cards: [{ id: 5, text: 'Task 5' },{ id: 6, text: 'Task 6' }] }
      ],
      newListTitle: '',
      newCardText: [],
      cardIdCounter: 6,
      dropHandled: false
    };
  },
  watch: {
    // Observar cambios en la lista y guardarla en localStorage
    lists: {
      handler(newLists) {
        localStorage.setItem('taskLists', JSON.stringify(newLists));
      },
      deep: true
    }
  },
  methods: {
    addList() {
      if (this.newListTitle.trim() !== '') {
        this.lists.push({ id: this.lists.length + 1, title: this.newListTitle, cards: [] });
        this.newListTitle = '';
      }
    },
    addCard(listIndex) {
      const newText = this.newCardText[listIndex].trim();
      if (newText !== '') {
        const newCard = { id: this.cardIdCounter++, text: newText };
        this.lists[listIndex].cards.push(newCard);
        this.newCardText[listIndex] = '';
      }
    },
    deleteList(listIndex) {
      this.lists.splice(listIndex, 1);
    },
    deleteAllLists() {
      this.lists = [];
    },
    deleteCard(listIndex, cardIndex) {
      this.lists[listIndex].cards.splice(cardIndex, 1);
    },
    drag(card, listIndex, cardIndex) {
      event.dataTransfer.setData('card', JSON.stringify({ card, listIndex, cardIndex }));
    },
    dropCard(targetListIndex) {
      if (!this.dropHandled) {
        const data = JSON.parse(event.dataTransfer.getData('card'));
        this.lists[targetListIndex].cards.push(data.card);
        this.lists[data.listIndex].cards.splice(data.cardIndex, 1);
        this.dropHandled = true;
        setTimeout(() => {
          this.dropHandled = false;
        }, 0);
      }
    },
    dropList(targetListIndex) {
      if (!this.dropHandled) {
        const data = JSON.parse(event.dataTransfer.getData('card'));
        if (targetListIndex !== data.listIndex) {
          this.lists[targetListIndex].cards.push(data.card);
          this.lists[data.listIndex].cards.splice(data.cardIndex, 1);
        }
        this.dropHandled = true;
        setTimeout(() => {
          this.dropHandled = false;
        }, 0);
      }
    }
  }
};
</script>
<style scoped lang="scss">
@import url("../styles/desktopStyle.scss");
</style>




