<template>
  <div>
    <v-app>
      <v-app-bar dense app color="primary" class="white--text">
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
        <v-alert v-if="!input.check" type="error"
          >タスクを入力してください！</v-alert
        >
        <section>
          <!-- todo -->
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
            :items="todos"
            :items-per-page="10"
            class="elevation-1"
          >
            <!-- 完了、未完了チェックボックス切り替え機能ここから -->
            <template v-slot:[`item.check`]="{ item }">
              <v-simple-checkbox v-model="item.isDone"></v-simple-checkbox>
            </template>
            <!-- 完了、未完了チェックボックス切り替え機能ここまで -->

            <!-- タスク名編集機能ここから -->
            <template v-slot:[`item.task`]="props">
              <v-edit-dialog
                :return-value.sync="props.item.task"
                large
                persistent
                @save="save"
                @cancel="cancel"
                @open="open"
                @close="close"
              >
                <div>{{ props.item.task }}</div>
                <template v-slot:input>
                  <div class="mt-4 text-h6">
                    Update Iron
                  </div>
                  <v-text-field
                    v-model="props.item.task"
                    :rules="[max25chars]"
                    label="Edit"
                    single-line
                    counter
                    autofocus
                  ></v-text-field>
                </template>
              </v-edit-dialog>
            </template>
            <!-- タスク名編集機能ここまで -->

            <!-- 詳細内容編集機能ここから -->
            <template v-slot:[`item.detail`]="props">
              <v-edit-dialog
                :return-value.sync="props.item.detail"
                large
                persistent
                @save="save"
                @cancel="cancel"
                @open="open"
                @close="close"
              >
                <div>{{ props.item.detail }}</div>
                <template v-slot:input>
                  <div class="mt-4 text-h6">
                    Update Iron
                  </div>
                  <v-text-field
                    v-model="props.item.detail"
                    :rules="[max25chars]"
                    label="Edit"
                    single-line
                    counter
                    autofocus
                  ></v-text-field>
                </template>
              </v-edit-dialog>
            </template>
            <!-- 詳細内容編集機能ここまで -->

            <!-- タスク削除機能ここから -->
            <template v-slot:[`item.actions`]="{ item }">
              <v-icon small @click="deleteTask(item)">
                mdi-delete
              </v-icon>
            </template>
            <!-- タスク削除機能ここまで -->
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
// ■TODOの編集機能
// ■TODOのタイトル編集機能
// ■TODOのステータス（完了／未完了）の切替機能

// 【余裕があればやってみてください】
// ▼ソート、フィルター、期限設定の機能など自分で思いついた機能を追加する。
// □タスクの完了状態に合わせてデザインを適用する

export default {
  data: function() {
    return {
      todos: [],
      headers: [
        { text: "check", value: "check" },
        { text: "id", value: "id" },
        { text: "task", value: "task" },
        { text: "detail", value: "detail" },
        { text: "actions", value: "actions" }
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
        task: this.input.text,
        detail: "-"
      };

      // タスクの追加
      this.todos.push(newTask);
      // テキスト入力フィールドを空にする
      this.input.text = "";
    },
    // 削除ボタンによりタスクを削除する
    deleteTask(index) {
      console.log("index:" + index);
      this.todos.splice(index, 1);
    }
  },
  computed: {
    filteredList() {
      // 【自動処理：ID再振り分け】
      // タスクを削除した際に、todo配列のidを再度振り分ける
      // for (let i = 0; i < this.todos.length; i++) {
      //   this.todos[i].id = i + 1;
      // }
      // let sortedTodos = this.todos;

      // 【自動処理：未完了、完了の自動ソート】
      // 未完了タスクを配列に格納する
      // let sortedTodos = [];
      // for (let i = 0; i < this.todos.length; i++) {
      //   if (this.todos[i].isDone === false) {
      //     sortTodos.push(this.todos[i]);
      //   }
      // }

      // 完了タスクを配列に格納する
      // for (let i = 0; i < this.todos.length; i++) {
      //   if (this.todos[i].isDone === true) {
      //     sortTodos.push(this.todos[i]);
      //   }
      // }

      // 最終的なtodo配列を返却する
      return this.todos;
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
