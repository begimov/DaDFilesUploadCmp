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
      <input type="file" name="files[]" id="file" class="dnd__input" multiple v-on:change="selected" ref="fileInput">
      <label for="file" class="dnd__label">
          <strong>
              Перенесите сюда файлы или кликните, чтобы их выбрать 
          </strong>
      </label>

      <uploads v-if="files.length"/>
  </div>
</template>

<script>
import Uploads from './Uploads'
export default {
  data() {
    return {
      files: [],
      isDraggedOver: false
    };
  },
  components: {
    Uploads,
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
      Array.from(files).forEach(file => {
        this.storeMeta(file).then(
          fileObj => {
            this.upload(fileObj);
          },
          fileObj => {
            fileObj.failed = true;
          }
        );
      });
    },
    upload(fileObj) {
      const form = new FormData();
      form.append("file", fileObj.file);
      form.append("id", fileObj.id);

      // emit upload event
      axios.interceptors.request.use(function(config) {
        fileObj.xhr = config;
        return config;
      });

      axios
        .post("http://localhost:8000/upload.php", form, {
          onUploadProgress: function(e) {
            // emit progress
            console.log(e.loaded);
          }
        })
        .then(
          res => {
            // emit finished
          },
          () => {
            // emit failed
          }
        );
    },
    storeMeta(file) {
      const fileObj = this.generateFileObj(file);
      return new Promise((resolve, reject) => {
        axios
          .post("http://localhost:8000/store.php", {
            name: file.name
          })
          .then(
            res => {
              fileObj.id = res.data.data.id;
              resolve(fileObj);
            },
            () => {
              reject(fileObj);
            }
          );
      });
    },
    generateFileObj(file) {
      const fileObjIndex =
        this.files.push({
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

