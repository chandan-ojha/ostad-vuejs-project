<script setup>
import { ref, reactive, onBeforeMount } from "vue";
import WishListIcon from "./WishListIcon.vue";
import { authStore } from "../store/authStore";
import { cartStore } from "../store/cartStore";
import { wishlistStore } from "../store/wishlistStore";
import axios from "axios";
const products = ref([]);
const auth = authStore;
const cart = cartStore;
const wishlist = wishlistStore;

function getSlug(title) {
  return title.toLowerCase().replace(/\s+/g, "-");
}

onBeforeMount(() => {
  axios
    .get("http://localhost:8000/api/products")
    .then((res) => {
      products.value = res.data;
    })
    .catch((err) => {
      console.log(err);
    });

  wishlist.fetchWishlist();
});
</script>
<template>
  <div class="bg-white">
    <div class="mx-auto px-12 py-8">
      <h2 class="text-2xl font-bold tracking-tight text-gray-900">Products</h2>
      <p>
        {{ wishlist.items }}
      </p>
      <div
        class="mt-6 grid grid-cols-1 gap-x-6 gap-y-10 sm:grid-cols-2 lg:grid-cols-4 xl:gap-x-20"
      >
        <div
          v-for="product in products"
          :key="product.id"
          class="group relative"
        >
          <div
            class="aspect-h-1 aspect-w-1 w-full overflow-hidden rounded-md bg-gray-200 lg:aspect-none group-hover:opacity-75 lg:h-80"
          >
            <router-link :to="{ name: 'product', params: { id: product.id } }">
              <img
                :src="product.image"
                class="h-full w-full object-cover object-center lg:h-full lg:w-full"
              />
            </router-link>
          </div>
          <div class="mt-4 flex justify-between">
            <div>
              <h3 class="text-sm text-gray-700">
                <router-link
                  :to="{ name: 'product', params: { id: product.id } }"
                >
                  {{ product.title }}
                </router-link>
              </h3>
            </div>
            <p class="text-sm font-medium text-gray-900">
              ${{ product.price }}
            </p>
          </div>
          <div class="flex justify-between items-center">
            <button
              @click="cart.addItem(product)"
              class="mt-2 bg-blue-500 hover:bg-blue-700 text-white text-sm py-2 px-4 rounded"
            >
              Add To Cart
            </button>
            <!-- <img
              @click="wishlist.toggleWishlist(product)"
              class="w-8 cursor-pointer"
              :src="wishlist.getIcon(product)"
              alt=""
            /> -->
            <!-- <WishListIcon :product="product" /> -->
            <wish-list-icon :product="product" v-if="auth.isAuthenticated" />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style></style>
