<template>
  <div class="head">
    <div class="child">
      <SearchField @pickedResult="this.pickedRessult" />
      <SearchButton :logRes="pickedResToBtn" />
    </div>
  </div>
</template>

<script>
import SearchField from "./SearchField.vue";
import SearchButton from "./SearchButton.vue";

export default {
  data() {
    return {
      pickedResToBtn: "",
    };
  },
  components: {
    SearchField,
    SearchButton,
  },
  methods: {
    pickedRessult(e) {
      this.pickedResToBtn = e;
    },
  },
};
</script>

<style>
.head {
  display: flex;
  height: 4.5em;
  background-image: linear-gradient(to right, #ff690f, #e8381b);
}

.child {
  display: flex;
  width: 100%;
  margin-top: 1rem;
  align-items: flex-start;
  justify-content: center;
}
</style>
