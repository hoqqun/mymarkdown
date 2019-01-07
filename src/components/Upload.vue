<template>
  <div class="drop" type="file" multiple="multiple" @dragleave.prevent @dragover.prevent @drop.prevent="onDrop">
    <div class="drop__default-container">
      <label>ファイルを選択
        <input class="drop__input" type="file" multiple="multiple" @change="onDrop">
      </label>
    </div>
    <div class="file_names">
      <ul>
        <li v-for="(file, index) in files">
          {{ file.name }}
          <button @click="deleteFile(index)">削除</button>
        </li>
      </ul>
    </div>
  </div>
</template>
 
<script>
export default {
  data() {
      return {
          isDrag: null,
          files: []
      }
  },
  methods: {
    onDrop: function(event) {
      let fileList = event.target.files.length ? event.target.files : event.dataTransfer.files;

      for(let i = 0; i < fileList.length; i++){
        this.files.push(fileList[i]);
      }

      console.log(this.files);
    },
    deleteFile: function(index) {
      this.files.splice(index, 1);
    }
  }
};
</script>
