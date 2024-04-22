<template>
  <div class="desktop">
    <div class="lists-container">
      <div v-for="(list, listIndex) in lists" :key="listIndex" class="list" ref="lists" @drop="dropList(listIndex)" @dragover.prevent>
        <div class="list-header">
          <h2>{{ list.title }}</h2>
          <button @click="deleteList(listIndex)">Delete List</button>
        </div>
        <div class="cards-container" ref="cards">
          <div v-for="(card, cardIndex) in list.cards" :key="cardIndex" class="card" draggable="true" @dragstart="drag(card, listIndex, cardIndex)">
            <div class="card-content">
              <span>{{ card.text }}</span>
              <button @click="deleteCard(listIndex, cardIndex)">Delete</button>
            </div>
          </div>
        </div>
        <div class="add-card">
          <input type="text" v-model="newCardText[listIndex]" @keyup.enter="addCard(listIndex)" placeholder="Enter task">
        </div>
      </div>
    </div>
    <div class="add-list">
      <input type="text" v-model="newListTitle" @keyup.enter="addList" placeholder="Add new list">
    </div>
    <button @click="deleteAllLists">Delete All Lists</button>
  </div>
</template>

<script>
export default {
  name: 'Desktop',
  data() {
    return {
      lists: [
        { id: 1, title: 'To Do', cards: [{ id: 1, text: 'Task 1' }, { id: 2, text: 'Task 2' }] },
        { id: 2, title: 'In Progress', cards: [{ id: 3, text: 'Task 3' }] },
        { id: 3, title: 'Done', cards: [{ id: 4, text: 'Task 4' }] }
      ],
      newListTitle: '',
      newCardText: [],
      cardIdCounter: 5,
      dropHandled: false
    };
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

<style>
.desktop {
  display: flex;
  justify-content: space-around;
  margin-top: 20px;
}

.lists-container {
  display: flex;
}

.list {
  background-color: #050505;
  border-radius: 5px;
  margin-right: 20px;
  padding: 10px;
}

.cards-container {
  margin-top: 10px;
}

.card {
  background-color: #992525;
  border-radius: 8px;
  margin-bottom: 5px;
  padding: 5px;
}

.add-card input {
  width: 100%;
  padding: 5px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 3px;
}

.add-list input {
  width: 200px;
  padding: 5px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 3px;
}
</style>