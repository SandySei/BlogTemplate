<script>
import { RouterLink } from "vue-router";

export default {
  props: {
    posts: Array,
  },
  data() {
    return {
      search: "",
      showModal: false,
      selectedPost: null,
    };
  },
  computed: {
    filteredPosts() {
      // se search estiver vazia, retorne a lista completa de posts
      if (!this.search) return this.posts;

      //se tiver qualquer coisa em search, faz o filtro
      const listaFiltrada = [];

      for (const post of this.posts) {
        if (post.title.includes(this.search)) {
          listaFiltrada.push(post);
        }
      }
      return listaFiltrada;
    },
  },
  methods: {
    getPostId(title) {
      for (const index in this.posts) {
        const post = this.posts[index];
        if (post.title == title) return index;
      }
    },
    setupModal(id) {
      this.showModal = !this.showModal;

      if (id) {
        this.selectedPost = this.posts[id];
        return;
      }
      this.selectedPost = null;
    },
    deletePost() {
      const id = this.getPostId(this.selectedPost.title);
      this.$emit("delete-post", id);

      this.setupModal();
    },
  },
};
</script>

<template>
  <input
    class="text"
    v-model="search"
    placeholder="Procure pelo título do post..."
  />

  <div id="lista-posts">
    <div class="post" v-for="(post, index) in filteredPosts" :key="post.key">
      <div class="flex">
        <RouterLink :to="`/detail/${getPostId(post.title)}`">
          <h3>
            {{ post.title }}
          </h3>
        </RouterLink>

        <section class="icons">
          <RouterLink :to="`/detail/${getPostId(post.title)}`"
            ><span class="material-symbols-outlined">
              visibility
            </span></RouterLink
          >

          <RouterLink :to="`/edit/${getPostId(post.title)}`">
            <span class="material-symbols-outlined" h>
              edit_note
            </span></RouterLink
          >
          <span
            class="material-symbols-outlined"
            @click="setupModal(getPostId(post.title))"
          >
            delete
          </span>
        </section>
      </div>

      <p>{{ post.content }}</p>

      <h4>{{ post.datetime }} <br /><br /></h4>
    </div>
  </div>
  <div class="modal" v-show="showModal">
    <div class="modal-content">
      <h3>Deletar Post</h3>
      <p>Tem certeza que quer deletar o post '{{ selectedPost?.title }}'?</p>

      <div class="modal-actions">
        <button class="bg-error" @click="setupModal">Cancelar</button>
        <button class="bg-success" @click="deletePost">Confirmar</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
h4 {
  margin-right: 15px;

  color: #000000;
  text-decoration: none;
  text-align: end;
  font-size: 14px;
  font-weight: 300;
}
</style>
