<template>
  <div
  class="dnd"
  @dragover.prevent="enter"
  @dragenter.prevent="enter"
  @dragleave.prevent="leave"
  @dragend.prevent="leave"
  @drop.prevent="drop"
  v-bind:class="{ 'dnd--dragged': isDraggedOver }"
  >
  {{ isDraggedOver }}
      <input type="file" name="files[]" id="file" class="dnd__input" multiple v-on:change="selected" ref="fileInput">
      <label for="file" class="dnd__label">
          <strong>
              Перенесите сюда файлы или кликните, чтобы их выбрать 
          </strong>
      </label>
  </div>
</template>

<script>
export default {
  data() {
    return {
      files: [],
      isDraggedOver: false
    };
  },
  methods: {
    enter() {
      this.isDraggedOver = true;
    },
    leave() {
      this.isDraggedOver = false;
    },
    drop(e) {
      this.leave();
      this.addFiles(e.dataTransfer.files);
    },
    selected(e) {
      this.addFiles(this.$refs.fileInput.files);
    },
    addFiles(files) {
      Array.from(files).forEach(function(file) {
        this.storeMeta(file).then(
          fileObject => {
            // upload
          },
          fileObject => {
            // failed
          }
        );
      });
    },
    storeMeta(file) {
      // create file object
      return new Promise((resolve, reject) => {
        this.$http
          .post("http://localhost:8080/vueupload/store.php", {
            name: file.name
          })
          .then(
            res => {
              // set id on file object
              // resolve
            },
            () => {
              // reject
            }
          );
      });
    },
    generateFileObj(file) {
      const fileObjIndex = this.files.push({
        id: null,
        file: file,
        progress: 0,
        failed: false,
        loadedBytes: 0,
        totalBytes: 0,
        timeStarted: new Date().getTime(),
        secondsRemaining: 0,
        finished: false,
        cancelled: false,
        xhr: null
      }) - 1;
      return this.files[fileObjIndex];
    }
  }
};
</script>

<style scoped>
.dnd {
  --dnd-min-height: 200px;
  width: 100%;
  min-height: var(--dnd-min-height);
  background-color: #f9f9f9;
  position: relative;
  border: 2px dashed #ddd;
}
.dnd--dragged {
  border-color: #333;
}
.dnd__input {
  display: none;
}
.dnd__label {
  display: block;
  text-align: center;
  margin: calc(var(--dnd-min-height) / 2) 20px;
}
.dnd__label:hover {
  text-decoration: underline;
  cursor: pointer;
}
.dnd__label--compact {
  margin: calc(var(--dnd-min-height) / 4) 20px;
}
</style>

