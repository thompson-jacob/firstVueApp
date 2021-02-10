<template>
  <div class="home">
    <h1>{{ message }}</h1>
    <h1>New product</h1>
    Name:
    <input type="text" v-model="newProductName" />
    Price:
    <input type="text" v-model="newProductPrice" />
    Description
    <input type="text" v-model="newProductDescription" />
    Image Url:
    <input type="text" v-model="newProductImageUrl" />
    <button v-on:click="createProduct()">Make New Product</button>

    <div v-for="product in products" v-bind:key="product.id">
   
      <h2>Name: {{ product.name }}</h2>
      <p>Price: {{ product.price }}</p>
      <p>Description: {{ product.description }}</p>
      <img v-bind:src="product.image_url" alt="product image" />
      <div>
        <button v-on:click="showProduct(product)">More info</button>
      </div>
    </div>

    <dialog id="product-details">
      <form method="dialog">
        <h1>Product Info</h1>
        <p>
          Name:
          <input type="text" v-model="currentProduct.name" />
        </p>
        <p>
          price:
          <input type="text" v-model="currentProduct.price" />
        </p>
        <p>
          description:
          <input type="text" v-model="currentProduct.description" />
        </p>
        
        
        <button v-on:click="updateproduct(currentProduct)">Update</button>
        <button v-on:click="destroyProduct(currentProduct)">Destroy</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>
<style>

</style>
<script>
import axios from "axios";
export default {
  data: function() {
    return {
      message: "Here are some products for you to peruse!",
      products: [],
      newProductName: "",
      newProductPrice: "",
      newProductDescription: "",
      newProductImageUrl: "",
      currentProduct: {},
    };
  },
  created: function() {
    this.indexProducts();
  },
  methods: {
    // Create rest of CRUD app
    indexProducts: function() {
      axios.get("/api/products").then(response => {
        this.products = response.data;
        console.log("All Products:", this.products);
      });
    },
    createProduct: function() {
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        imageUrl: this.newProductImageUrl,
      };
      axios
        .post("/api/products", params)
        .then(response => {
          console.log("Great Success!", response.data);
          this.products.push(response.data);
        })
        .catch(error => console.log(error.response));
        this.newProductName = '';
        this.newProductPrice = '';
        this.newProductDescription = '';
        this.newProductImageUrl = '';
    },
    showProduct: function(product) {
      console.log("showProduct", product.title, product);
      this.currentProduct = product;
      document.querySelector("#product-details").showModal();
    },
    updateProduct: function(product) {
      var params = {
        name: product.name,
        price: product.price,
        description: product.description,
        imageUrl: product.imageUrl,
    };
    axios.patch(`/api/products/${product.id}`, params).then(response => {
      console.log("Update Success", response.data);
    });
    },
    destroyProduct: function(product) {
      axios.delete(`/api/products/${product.id}`).then(response => {
        console.log("Product Destroyed", response.data);
        var index = this.products.indexOf(product);
        this.products.splice(index, 1);
      });
    },
  },
};
</script>
