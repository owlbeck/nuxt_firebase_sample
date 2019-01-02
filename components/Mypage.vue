<template>
  <div id="mypage">
    <span>こんにちは, {{ user.displayName }}さん</span>
    <h2>あなたのノート</h2>
    <div id="notesIndex" v-for="note in notes" :key="note.content">
      <p class="noteContent">{{ note.content }}</p>
    </div>
    <h2>公開ノート</h2>
    <div id="notesIndex" v-for="public_note in public_notes" :key="public_note.content">
      <p class="noteContent">{{ public_note.content }}</p>
    </div>
    <p>
      <textarea v-model="note_content"></textarea>
    </p>
    <p>
      <button @click="saveContent(note_content)">ノートを保存する</button>
    </p>
    <p>
      <button @click="savePublicContent(note_content)">公開ノートに保存する</button>
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
      notes: [],
      public_notes: []
    };
  },
  created: function() {
    this.getContent();
    this.getPublicContent();
  },
  methods: {
    // ログアウト
    logout: function() {
      firebase.auth().signOut();
    },
    // 自分のノートを取得する
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
    // 公開ノートを取得する
    getPublicContent() {
      firebase
        .database()
        .ref("notes/public")
        .once("value")
        .then(result => {
          if (result.val()) {
            this.public_notes = result.val();
          }
        });
    },
    // 自分のノートにコンテンツを保存する
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
    },
    // 公開ノートにコンテンツを保存する
    savePublicContent: function(value) {
      var newNoteKey = firebase
        .database()
        .ref()
        .child("notes")
        .push().key;
      firebase
        .database()
        .ref("notes/public/" + newNoteKey)
        .set({
          content: value,
          uid: this.user.uid
        });

      this.getPublicContent();
    }
  }
};
</script>