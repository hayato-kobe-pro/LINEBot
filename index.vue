<template>
  <div>
  
  </div>
</template>
<script>
// firebase モジュールのインポート
import firebase from "firebase";
export default {
  data() {
    return {
      db: null, // created で初期化
      input: "", // テキストを入力した時に v-model でデータを保存
      inputDocRef: "", // 保存したデータのIDを入れる（1件だけ取得する時に使う）
      output: "", // submit した後にDBからデータを取得してきたものを保存
      outputAll: [] // 全件取得した時に全件分のデータを入れる
    };
  },
  created: function() {
    this.db = firebase.firestore(); // dbインスタンスを初期化
  },
  methods: {
    // DBにデータを送信する
    submit: function() {
      // この先にあるthenでthisの参照が切れるのでselfに退避させておく
      let self = this;
      // 先程作った「sample」というコレクションを取得する
      let collection = this.db.collection("sample");
      // 「sample」というコレクションに対して {} で定義した情報を add する
      collection
        .add({
          text: this.input
        })
        // .get()
        // .then(querySnapshot => {
        //   self.outputAll = [];
        //   querySnapshot.forEach(doc => {
        //     console.log(`${doc.id} => ${doc.data()}`);
        //     self.outputAll.push(doc.data());
        //   });
        // })
        .then(function(docRef) {
          // 保存に成功した時
          console.log("Document written with ID: ", docRef.id);
          // 1件だけ取得する処理のためにIDを保存しておく
          self.inputDocRef = docRef.id;
          docRef
            .get()
            .then(function(doc) {
              if (doc.exists) {
                console.log("Document data:", doc.data());
                self.output = doc.data().text;
              } else {
                // doc.data() will be undefined in this case
                console.log("No such document!");
              }
            })
            .catch(function(error) {
              console.log("Error getting document:", error);
            });
        })
        .catch(function(error) {
          // 保存に失敗した時
          console.error("Error adding document: ", error);
        });
    },
    // 1件だけ取得する
    get: function() {
      // この先にあるthenでthisの参照が切れるのでselfに退避させておく
      let self = this;
      // 直前に保存したデータを1件取得してくる
      let collection = this.db.collection("sample");
      let docRef = collection.doc(this.inputDocRef);
    },
    // 全件取得する
    getAll: function() {
      // この先にあるthenでthisの参照が切れるのでselfに退避させておく
      let self = this;
      // 全件取得する
      let collection = this.db.collection("sample");
    }
  }
};
</script>