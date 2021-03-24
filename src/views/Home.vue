<template>
  <div class="home">
    <div>
      <div v-for="product in products" v-bind:key="product.id">
        <h2>{{ product.name }}</h2>
        <img v-bind:src="product.image_url" v-bind:alt="product.name">
        <p class="price">${{ product.price }}</p>
      </div>
    </div>
  </div>
</template>

<style>
.price {
  margin-top: 5px;
  margin-bottom: 50px;
}

img {
  width: 300px;
}
</style>

<script>
import axios from "axios"; //this connects to rails

export default {
  data: function () {
    return {
      products: [],
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("/api/products").then((response) => {
        this.products = response.data;
        console.log(response.data);
      });
    },
  },
};
</script>
