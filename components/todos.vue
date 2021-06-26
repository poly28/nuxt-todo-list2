<template>
  <div>
    <v-app>
      <v-app-bar dense app color="green">
        <v-app-bar-title class="ml-5">
          Todoリスト
        </v-app-bar-title>
      </v-app-bar>
      <v-main>
        <v-form @submit.prevent="addTask">
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="3">
                <v-text-field
                  v-model="input.text"
                  label="Add Todo"
                  placeholder="put on your task"
                  clearable
                  append-icon="mdi-plus-thick"
                  @click:append="addTask"
                  @blur="input.check = true"
                >
                </v-text-field>
              </v-col>
            </v-row>
          </v-container>
        </v-form>
        <v-alert v-show="!input.check" type="error"
          >タスクを入力してください！</v-alert
        >
        <!-- <v-divider></v-divider> -->

        <section>
          <!-- todo -->
          <!-- expantion panelの高度なカスタマイズ実装-->
          <!-- https://vuetifyjs.com/ja/components/expansion-panels/#section-9ad85ea6306a4f7f304465b9 -->
          <!-- <v-expansion-panels>
            <v-expansion-panel
              v-for="(todo, index) in filteredList"
              :key="todo.id"
            >
              <v-expansion-panel-header>
                <span :class="{ done: todo.isDone }">
                  <input type="checkbox" v-model="todo.isDone" />
                  <template>
                    {{ todo.task }}
                  </template>
                  <v-btn icon="mdi-plus-thick"></v-btn>
                  <button @click="deleteTask(index)">
                    削除
                  </button>
                </span>
              </v-expansion-panel-header>
              <v-expansion-panel-content> - </v-expansion-panel-content>
            </v-expansion-panel>
          </v-expansion-panels> -->
          <!-- <ul>
            <li v-for="(todo, index) in filteredList" :key="todo.id">
              <span :class="{ done: todo.isDone }">
                <input type="checkbox" v-model="todo.isDone" />
                {{ todo.id }} ： {{ todo.task }}
              </span>
              <button class="delete" @click="deleteTask(index)">×</button>
            </li>
          </ul> -->

          <v-data-table
            :headers="headers"
            :items="filteredList"
            :items-per-page="10"
            class="elevation-1"
          >
            <template></template>
          </v-data-table>
        </section>
        <!-- デバッグ -->
        <pre>{{ $data }}</pre>
        <!-- デバッグ -->
      </v-main>
    </v-app>
  </div>
</template>

<script>
// ▼各TODOにほしい要素
// ■ID（一意のキーを設定）
// ■タイトル
// ■ステータス（完了／未完了）

// ▼ほしい機能
// ■TODOの追加・削除
// □TODOの編集機能
// □TODOのタイトル編集機能
// ■TODOのステータス（完了／未完了）の切替機能

// 【余裕があればやってみてください】
// ▼ソート、フィルター、期限設定の機能など自分で思いついた機能を追加する。

export default {
  data: function() {
    return {
      todos: [],
      headers: [
        { text: "check", value: "check" },
        { text: "id", value: "id" },
        { text: "task", value: "task" },
        { text: "detail", value: "detail" }
      ],
      input: {
        text: "",
        check: true
      }
    };
  },
  methods: {
    // 追加ボタンにより新規タスクを追加する
    addTask() {
      // 未入力バリデーション
      if (this.input.text === "") {
        this.input.check = false;
        return;
      }
      // タスク入力があればバリデーション回避
      this.input.check = true;

      let newTask = {
        isDone: false,
        id: this.todos.length + 1,
        task: this.input.text
      };

      // タスクの追加
      this.todos.push(newTask);
      // テキスト入力フィールドを空にする
      this.input.text = "";
    },
    // 削除ボタンによりタスクを削除する
    deleteTask(index) {
      this.todos.splice(index, 1);
    }
  },
  computed: {
    filteredList() {
      let idTodos = this.todos;

      // 【自動処理：ID再振り分け】
      // タスクを削除した際に、todo配列のidを再度振り分ける
      for (let i = 0; i < idTodos.length; i++) {
        idTodos[i].id = i + 1;
      }

      // 【自動処理：未完了、完了の自動ソート】
      // 未完了タスクを配列に格納する
      // let sortTodos = [];
      // for (let i = 0; i < idTodos.length; i++) {
      //   if (idTodos[i].isDone === false) {
      //     sortTodos.push(idTodos[i]);
      //   }
      // }

      // 完了タスクを配列に格納する
      // for (let i = 0; i < idTodos.length; i++) {
      //   if (idTodos[i].isDone === true) {
      //     sortTodos.push(idTodos[i]);
      //   }
      // }

      // 最終的なtodo配列を返却する
      return idTodos;
    }
  }
};
</script>

<style lang="scss" scoped>
.input {
  border: 1px solid black;
}
.done {
  text-decoration: line-through;
  color: gray;
}
.delete {
  border: 1px solid black;
}
</style>
