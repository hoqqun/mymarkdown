<template>
  <div class="editor">
    <h1>エディター画面</h1>
    <span>{{ user.displayName }}</span>
    <button @click="logout">ログアウト</button>
    <div class="editorWrapper">
      <div class="memoListWrapper">
        <div class="memoList" v-for="(memo, index) in memos" :key="index" @click="selectMemo(index)" :data-selected="index == selectedIndex">
          <p class="memoTitle"> {{ displayTitle(memo.markdown) }} </p>
        </div>
        <button class="addMemoBtn" @click="addMemo">メモの追加</button>
        <button class="deleteMemoBtn" @click="deleteMemo" v-if="memos.length > 1">選択中のメモの削除</button>
        <button class="saveMemosBtn" @click="saveMemos">メモの保存</button>
      </div>
      <textarea class="markdown" v-model="memos[selectedIndex].markdown"></textarea>
      <div class="preview markdown-body" v-html="preview()"></div>
    </div>
    <div class="drop" type="file" multiple="multiple" @dragleave.prevent @dragover.prevent @drop.prevent="onDrop">
      <div class="drop__default-container">
        <label>ファイルを選択
          <input class="drop__input" type="file" multiple="multiple" @change="onDrop">
        </label>
      </div>
      <div class="file_names">
        <ul>
          <li v-for="(name, index) in memos[selectedIndex].files_name">
            {{ name }}
            <button @click="deleteFile(index)">削除</button>
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
  import marked from "marked";
  import Upload from "./Upload.vue"

  export default {
    name: "editor",
    props: ["user"],
    data() {
      return {
        memos: [
          {
            markdown: "",
            files_name: []
          }
        ],
        selectedIndex: 0
      };
    },
    components: {
      Upload: Upload
    },
    created: function() {
      firebase.database().ref("memos/" + this.user.uid).once("value").then( result => {
        if (result.val()) {
          this.memos = result.val();
        }
      })
    },
    mounted: function() {
      document.onkeydown = e => {
        if (e.key == "s" && (e.metaKey || e.ctrlKey)) {
          this.saveMemos();
          return false;
        }
      }
    },
    beforeDestroy: function() {
      document.onkeydown = null;
    },
    methods: {
      logout: function() {
        firebase.auth().signOut();
      },
      addMemo: function () {
        this.memos.push({
          markdown: "無題のメモ",
          files_name: []
        });
      },
      deleteMemo: function() {
        this.memos.splice(this.selectedIndex, 1);

        if (this.selectedIndex > 0) {
          this.selectedIndex--;
        }
      },
      saveMemos: function() {
        storageRef = firebase.storage().ref();

        firebase.database().ref("memos/" + this.user.uid).set(this.memos);
      },
      saveMemo: function(memo) {
        
      },
      saveFiles: function(files) {

      },
      saveFile: function(file) {
        
      },
      selectMemo: function(index) {
        this.selectedIndex = index;
      },
      preview: function() {
        return marked(this.memos[this.selectedIndex].markdown);
      },
      displayTitle: function(text) {
        return text.split(/\n/)[0];
      },
      onDrop: function(event) {
        let fileList = event.target.files.length ? event.target.files : event.dataTransfer.files;

        for(let i = 0; i < fileList.length; i++){
          this.memos[this.selectedIndex].files_name.push(fileList[i].name);
        }
      },
      deleteFile: function(index) {
        this.memos[this.selectedIndex].files.splice(index, 1);
      }
    }
  };
</script>

<style lang="scss" scoped>
  .editorWrapper {
    display: flex;   
  }
  .memoListWrapper {
    width: 20%;
    border-top: 1px solid #000;
  }
  .memoList {
    padding: 10px;
    box-sizing: border-box;
    text-align: left;
    border-bottom: 1px solid #000;
    &:nth-child(even) {
      background-color: #ccc;
    }
    &[data-selected="true"] {
      background-color: #ccf;
    }
  }
  .memoTitle {
    height: 1.5em;
    margin: 0;
    white-space: nowrap;
    overflow: hidden;
  }
  .addMemoBtn {
    margin-top: 20px;    
  }
  .deleteMemoBtn {
    margin: 10px;
  }

  .markdown {
    width: 40%;
    height: 500px;
  }
  .preview {
    width: 40%;
    text-align: left;
  }
  .upload {
    position: absolute;
    width: 80%;
  }
</style>
