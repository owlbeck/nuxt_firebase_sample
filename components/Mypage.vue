<template>
  <div id="mypage">
    <span>こんにちは, {{ user.displayName }}さん</span>
    <h2>あなたのノート</h2>
    <div id="notesIndex" v-for="note in notes" :key="note.content">
      <p class="noteContent">{{ note.content }}</p>
    </div>
    <p>
      <textarea v-model="note_content"></textarea>
    </p>
    <p>
      <button @click="saveContent(note_content)">ノートを保存する</button>
    </p>
    <button @click="logout">ログアウト</button>
  </div>
</template>

<script>
import firebase from "@/plugins/firebase";
export default {
  name: "mypage",
  props: ["user"],
  data(context) {
    return {
      note_content: "hello",
      notes: []
    };
  },
  created: function() {
    this.getContent();
  },
  methods: {
    // ログアウト
    logout: function() {
      firebase.auth().signOut();
    },
    // Firebaseからデータを取得する
    getContent() {
      firebase
        .database()
        .ref("notes/" + this.user.uid)
        .once("value")
        .then(result => {
          if (result.val()) {
            this.notes = result.val();
          }
        });
    },
    // Firebaseにコンテンツを保存する
    saveContent: function(value) {
      var newNoteKey = firebase
        .database()
        .ref()
        .child("notes")
        .push().key;
      firebase
        .database()
        .ref("notes/" + this.user.uid + "/" + newNoteKey)
        .set({ content: value });

      this.getContent();
    }
  }
};
</script>