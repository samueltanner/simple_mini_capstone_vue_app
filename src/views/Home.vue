<template>
  <div class="home">
    <div>
      <h2>Add Product</h2>
      <p>
        Product Name:
        <input type="text" v-model="newProductName" />
      </p>
      <p>
        Product Description:
        <input type="text" v-model="newProductDescription" />
      </p>
      <p>
        Product Image URL:
        <input type="text" v-model="newProductImageURL" />
      </p>
      <p>
        Product Price:
        <input type="text" v-model="newProductPrice" />
      </p>
      <button v-on:click="createProduct()">Add Product</button>
      <!-- <div v-for="error in errors" v-bind:key="error">
        <p>{{ error }}</p>
      </div> -->
    </div>

    <div>
      <div v-for="product in products" v-bind:key="product.id">
        <h2>{{ product.name }}</h2>
        <img v-bind:src="product.image_url" v-bind:alt="product.name" />
        <p class="price">${{ product.price }}</p>
        <button v-on:click="showProductInfo(product)">More Info</button>
      </div>
      <dialog id="product-info">
        <form method="dialog">
          <h1>Product Info</h1>
          <p>
            Product Name:
            <input type="text" v-model="currentProduct.name" />
          </p>
          <p>
            Product Description:
            <input type="text" v-model="currentProduct.description" />
          </p>
          <p>
            Product Price:
            <input type="text" v-model="currentProduct.price" />
          </p>
          <p>
            Product Image URL:
            <input type="text" v-model="currentProduct.image_url" />
          </p>
          <button v-on:click="updateProduct(currentProduct)">Update</button>
          <button v-on:click="destroyProduct(currentProduct)">Delete Product</button>
          <button>Close</button>
        </form>
      </dialog>

      <!-- <dialog id="error-window">
        <form method="dialog">
          <p>{{ errors }}</p>
        </form>
      </dialog> -->
    </div>
  </div>
</template>

<style>
/* .price {
  margin-top: 5px;
  margin-bottom: 50px;
} */

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
      newProductName: "",
      newProductDescription: "",
      newProductImageURL: "",
      newProductPrice: "",
      currentProduct: {},
      // errors: [],
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
    showProductInfo: function (product) {
      console.log(product);
      this.currentProduct = product;
      document.querySelector("#product-info").showModal();
    },
    createProduct: function () {
      // console.log("creating a product");
      var params = {
        name: this.newProductName,
        description: this.newProductDescription,
        image_url: this.newProductImageURL,
        price: this.newProductPrice,
      };
      axios
        .post("/api/products", params)
        .then((response) => {
          console.log(response.data);
          this.products.push(response.data);
          //this resets the fields to blank
          // this.newProductName = "";
          // this.newProductDescription = "";
          // this.newProductImageURL = "";
          // this.newProductPrice = "";
        })
        .catch((error) => {
          // this.errors.push(error.response);
          // document.querySelector("#error-window").showModal();
          console.log(error.response);
        });
    },
    updateProduct: function (product) {
      var params = {
        name: product.name,
        description: product.description,
        image_url: product.image_url,
        price: product.price,
      };
      axios.patch("/api/products/" + product.id, params).then((response) => {
        console.log("success", response.data);
      });
    },
    destroyProduct: function (product) {
      axios.delete("/api/products/" + product.id).then((response) => {
        console.log("product deleted", response.data);
        var index = this.products.indexOf(product); //this finds the index of the product
        this.products.splice(index, 1); //this pulls out product (index = product, 1 = the next product but not inclusive)
      });
    },
  },
};
</script>
