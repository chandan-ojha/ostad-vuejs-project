<script setup>
import { ref, reactive, onBeforeMount, watch, computed } from "vue";
import { useRoute } from "vue-router";
const route = useRoute();
import axios from "axios";
const posts = ref([]);
// const tag = ref(route.params.tag);
const tag = computed(() => {
  return route.params.tag;
});

function getSlug(title) {
  return title.toLowerCase().replace(/\s+/g, "-");
}

//fetch posts data
onBeforeMount(() => {
  // fetch("https://dummyjson.com/posts?limit=20")
  //   .then((res) => res.json())
  //   .then((data) => {
  //     posts.value = data.posts;
  //   });
  axios
    .get("https://dummyjson.com/posts?limit=10")
    .then((res) => {
      posts.value = res.data.posts;
    })
    .catch((err) => {
      console.log(err);
    });
});

//load more posts
function loadMore() {
  axios
    .get("https://dummyjson.com/posts?limit=20&skip=10")
    .then((res) => {
      posts.value = [...posts.value, ...res.data.posts];
    })
    .catch((err) => {
      console.log(err);
    });
}

//watch tag
// watch(
//   () => route.params.tag,
//   (newValue) => {
//     tag.value = newValue;
//   }
// );
</script>
<template>
  <div class="my-5">
    {{ tag }}
  </div>
  <article class="mb-10" v-for="post in posts" :key="post.id">
    <h1 class="text-xl mb-2">
      <!-- <router-link :to="{ name: 'post', params: { id: getSlug(post.title) } }">{{ post.title }}</router-link> -->
      <router-link :to="{ name: 'post', params: { id: post.id } }">
        {{ post.title }}
      </router-link>
    </h1>
    <p>
      <router-link :to="{ name: 'post', params: { id: post.id } }">
        <img :src="`//source.unsplash.com/random/300x200?${post.id}`" alt="" />
      </router-link>
      {{ post.body }}
    </p>
  </article>
  <button
    @click="loadMore()"
    class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
    v-if="posts.length > 0"
  >
    Load More
  </button>
</template>

<style></style>
