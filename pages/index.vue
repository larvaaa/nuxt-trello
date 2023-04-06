<template>
  <div>
    <el-container>
      <el-header>
        <el-row>
          <el-button type="info" plain @click="addTodoForm">+ Add another list</el-button>
        </el-row>
      </el-header>
      <el-main class="row_card">
        <el-row :gutter="20">
          <el-col v-for="(todo, todoIndex) in todos" :key="todoIndex" :span="6">
            <div v-if="todo.todoId" class="div_card">
              <el-input v-model="todo.todoTitle" type="textarea" autosize placeholder="Please input"></el-input>
              <div style="margin: 20px 0;"></div>

              <el-button type="primary" @click="updateTodo(todoIndex)">Update todo</el-button>
              <el-button type="success" @click="openAddCardForm(todo.todoId)">+ Add a card</el-button>
              <el-button type="danger" icon="el-icon-delete" circle @click="removeTodo(todo.todoId)"></el-button>

              <div style="margin: 20px 0;"></div>
              <div v-for="(card, cardIndex) in todo.cards" :key="cardIndex">
                <el-input
                   v-model="card.cardTitle" label placeholder="Please input"  
                  readonly="" @click.native="openUpdCardForm(card.cardId)"/>
                <el-button type="danger" icon="el-icon-delete" circle @click="removeCard(card.cardId)"></el-button>
              </div>
            </div>
            <div v-else class="div_card new">
              <el-input v-model="todo.todoTitle" type="textarea" autosize placeholder="Please input"></el-input>
              <div style="margin: 20px 0;"></div>

              <el-button type="primary" @click="addTodo(todoIndex)">add todo</el-button>
              <el-button type="danger" icon="el-icon-delete" circle @click="removeTodo()"></el-button>

              <div style="margin: 20px 0;"></div>
              <div v-for="(card, cardIndex) in todo.cards" :key="cardIndex">
                <el-input
                   v-model="card.cardTitle" label placeholder="Please input"  
                  readonly="" @click.native="openUpdCardForm(card.cardId)"/>
                <el-button type="danger" icon="el-icon-delete" circle @click="removeCard(card.cardId)"></el-button>
              </div>
            </div>
          </el-col>
        </el-row>

      </el-main>
    </el-container>
    <component
:is="pageName" :todo-id="props.todoId" 
      :card-id="props.cardId" @closeCardForm="closeCardForm"
      @reload="getTodos"/>
  </div>
</template>

<script>
import UpdCardForm from '@/components/UpdCardForm'
import AddCardForm from '@/components/AddCardForm'
export default {

  name: 'IndexPage',
  components: {
    UpdCardForm,
    AddCardForm
  },
  data() {
    return {
      div_card: "new",
      pageName: '',
      todos: [],
      props : {
        todoId: '',
        cardId: ''
      }
    }
  },
  watch: {
    todos () {
      // for(const i of this.todos) console.log(i);
    }
  },
  async mounted() {

    await this.getTodos();

  },
  methods: {
    async removeTodo(todoId) {
      
      if(todoId === undefined || todoId === '') {
        this.todos.shift();
        return false;
      }

      if(!confirm("todo 삭제하시겠습니까?")) return false;
      await this.$axios.delete(`http://localhost:3000/api/todo/${todoId}`)
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("todo 삭제했습니다");
          this.getTodos();
        } else {
          alert("error");
        }
      });
    },
    async removeCard(cardId) {
            
      if(!confirm("card 삭제하시겠습니까?")) return false;

      await this.$axios.delete(`http://localhost:3000/api/card/${cardId}`)
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("card 삭제했습니다");
          this.getTodos();
        } else {
          alert("error");
        }
      });
    },
    openUpdCardForm(id) {
      this.props.cardId = id;
      this.pageName = 'UpdCardForm';
    },
    openAddCardForm(id) {
      // todo 아이디 전달
      this.props.todoId = id;
      this.pageName = 'AddCardForm';

    },
    closeCardForm() {
      this.pageName = '';
    },
    async getTodos() {
      await this.$axios.get(
      `http://localhost:3000/api/todo`, 
      {},
      {"Content-Type":"application-json"})
      .then(res => {
        if(res.data.resultCd === 'S') {
          this.todos = res.data.todoList;
        } else {
          alert("error");
        }
      });
    },
    addTodoForm() {
      if(this.todos.length === 0) {
        this.todos.unshift({});
      } else if(Object.keys(this.todos[0]).length) {
        this.todos.unshift({});
      }
    },
    addCardForm(index) {
      // console.log(this.todos[index].cards.unshift());
      if(this.todos[index].cards === null) this.todos[index].cards = [];
      this.todos[index].cards.unshift({});
    },
    async addTodo(index) {


      const todoTitle = this.todos[index].todoTitle;
      if(todoTitle === '' || todoTitle === undefined || todoTitle === null) {
        alert("내용 입력 필수");
        return false;
      }

      await this.$axios.post(
        `http://localhost:3000/api/todo`, 
        {todoTitle, memberId: '8'},
        {"Content-Type":"application-json"})
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("todo 추가했습니다");
          this.getTodos();
        } else {
          alert("error");
        }
      });
    },
    async updateTodo(index) {

      const todoTitle = this.todos[index].todoTitle;
      const todoId = this.todos[index].todoId;

      await this.$axios.patch(`http://localhost:3000/api/todo/${todoId}`, {todoTitle},{"Content-Type":"application-json"})
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("todo 수정했습니다");
          this.getTodos();
        } else {
          alert("error");
        }
      });
    }
  
  },
  
}
</script>
<style scoped>
  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }
  .div_card {
    padding: 14px;
    background-color: #ebecf0;
    border-radius: 10px;
  }

  .div_card.new {
    padding: 14px;
    background-color: #2B4366;
    border-radius: 10px;
  }
</style>