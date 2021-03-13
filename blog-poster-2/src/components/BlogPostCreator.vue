<template>
  <div class="blog-post-creator rteditor">
    <div class="post-input">
      <div class="title-input">
        <error v-if="!title && formEntered"
          >Title must be more than 10 characters.</error
        >
        <Input v-model="title" placeholder="Please enter a title" />
      </div>
      <div class="cat-input">
        <error v-if="!category && formEntered"
          >Post must have a category.</error
        >
        <Select
          placeholder="Select a category"
          v-model="category"
          v-if="postCategories.length"
        >
          <Option
            v-for="(cat, index) in postCategories"
            :key="index"
            :value="cat.id"
            >{{ cat.label }}
          </Option>
        </Select>
      </div>
      <div class="image-input">
        <error v-if="!image_url && formEntered">Post must have an image.</error>
        <div class="inner-image-input">
          <img :src="image_url" width="40px" height="40px" v-if="image_url" />
          <Button
            @click="showImageModal = true"
            class="flex-center"
            type="primary"
            icon="ios-camera-outline"
            >Post image</Button
          >
          <Button @click="showPreview">See preview</Button>
        </div>
      </div>
    </div>
    <quill-editor
      v-model="content"
      ref="myQuillEditor"
      :options="editorOptions"
    />
    <div class="post-actions-row">
      <Button class="btn-delete" @click="del">Delete</Button>
      <Button type="primary" @click="save">Save</Button>
      <Button type="primary" @click="publish">Publish</Button>
    </div>
  </div>
</template>

<script>
import "quill/dist/quill.snow.css";
import { quillEditor } from "vue-quill-editor";
import { Button, Input, Select } from "iview";
import { mapGetters } from "vuex";
import Error from "./Error.vue";

export default {
  name: "HelloWorld",
  components: {
    Button,
    Input,
    Select,
    Error,
    quillEditor,
  },
  data: () => ({
    title: "",
    category: "",
    image_url: "",
    content: "",
    editorOptions: {
      debug: "info",
      placeholder: "Write your post...",
      readOnly: true,
      theme: "snow",
    },
    postCategories: [
      {
        id: 1,
        label: "NodeJS",
        img:
          "https://pluralsight.imgix.net/paths/path-icons/nodejs-601628d09d.png?w=200",
      },
      {
        id: 2,
        label: "JavaScript",
        img: "https://cdn.auth0.com/blog/js-fatigue/JSLogo.png",
      },
    ],
    showImageModal: false,
    formEntered: false,
    mounting: false,
  }),
  watch: {
    content(val) {
      if (!this.mounting) {
        this.$store.commit(
          "setDelta",
          this.$refs.myQuillEditor.quill.getContents()
        );
      }
      this.$store.commit("setContent", val);
      this.mounting = false;
    },
  },
  methods: {
    showPreview() {
      this.$router.replace("/preview");
    },
    del() {
      this.formEntered = true;
    },
    save() {
      this.formEntered = true;
    },
    publish() {
      this.formEntered = true;
    },
  },
  computed: {
    ...mapGetters(["delta", "contents"]),
  },
  mounted() {
    this.mounting = true;
    if (!this.content && this.contents) {
      this.content = this.contents;
    }
  },
};
</script>

<style>
.rteditor {
  margin: 0px 20px 0px 20px;
}

.cat-input,
.title-input {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.post-inputs {
  display: grid;
  width: 90%;
  grid-gap: 20px;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  padding: 15px 0px 15px 0px;
}

.ql-editor {
  height: 72vh;
}

.inner-image-input {
  display: flex;
  align-items: center;
}

.flex-center {
  display: flex;
  align-items: center;
  justify-content: center;
}

.post-actions-row {
  display: flex;
  justify-content: space-between;
  padding: 10px;
}

.ivu-icon-ios-camera-outline {
  font-size: 16px;
}

.btn-delete {
  color: #2d8cf0 !important;
  border: 1px solid #2d8cf0 !important;
}

.btn-delete:hover {
  color: red !important;
  border: 1px solid red !important;
}
</style>
