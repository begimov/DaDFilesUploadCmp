<template>
  <div class="dnd">
      <input type="file" name="files[]" id="file" class="dnd__input" multiple>
      <label for="file" class="dnd__label">
          <strong>
              Перенесите сюда файлы или кликните, чтобы их выбрать 
          </strong>
      </label>
  </div>
</template>

<script>
export default {
  //
};
</script>

<style scoped>
    .dnd {
        --dnd-min-height: 200px;
        width: 100%;
        min-height: var(--dnd-min-height);
        background-color: #F9F9F9;
        position: relative;
        border: 2px dashed #DDD;
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
</style>

