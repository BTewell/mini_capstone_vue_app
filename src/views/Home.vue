<template>
  <div class="home">
    <h1>{{ message }}</h1>  
    <hr>
    <input type="text" v-model="titleFilter" list="titles">
    <datalist id="titles">
      <option v-for="product in products">{{ product.name }}</option>
    </datalist>
    <button v-on:click="setSortAttribute('name')">Sort by name</button>
      <div v-for="product in orderBy(products, sortAttribute, sortAsc)">

      <p>{{ product.name }}</p>
      <p><img v-bind:src="product.image_url"></p>
      <button v-on:click="toggleInfo(product)">Show more info</button>
      <div v-if="product === currentProduct">
      <p>{{ product.description }}</p>
      <p>{{ product.price }}</p>
      <p>name: <input type="text" v-model="product.name"></p>
      <p>description: <input type="text" v-model="product.description"></p>
      <p>price: <input type="text" v-model="product.price"></p>
      <p>image_url: <input type="text" v-model="product.image_url"></p>
      <button v-on:click="updateProduct(product)">Update the product</button>
      <button v-on:click="deleteProduct(product)">Delete the product</button>
      </div>
      <hr>
    </div>
  </div>
</template>

<style>
  img {
    width: 200px;
  }
</style>

<script>
import axios from "axios";

import Vue2Filters from "vue2-filters";

export default {
  mixins: [Vue2Filters.mixin],
  data: function() {
    return {
      message: "Welcome to Vue.js!",
      products: [],
      sortAttribute: "name",
      titleFilter: "",
      sortAsc: 1,
      newProductName:"",
      newProductDescription:"",
      newProductPrice:"",
      newProductImage_url:"",
      currentProduct: {}
    };
  },
  created: function() {
    axios.get("/api/products").then(response => {
      this.products = response.data;
    });
  },
  methods: {
    makeProduct: function() {
      console.log('making the new product...');
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImage_url
      };
      axios.post("/api/products", params).then(response => {
        console.log(response);
        this.products.push(response.data);
      });
    },
    toggleInfo: function(theProduct) {
      if (this.currentProduct === theProduct) {
        // I am seeing the info already
        this.currentProduct = {};
      } else {
        // we cannot see the info
        this.currentProduct = theProduct;
      }
      console.log('toggling info...');
    },
    updateProduct: function(theProduct) {
      console.log(theProduct);
      console.log('updating the product...');
      var params = {
        name: theProduct.name,
        description: theProduct.description,
        price: theProduct.price,
        image_url: theProduct.image_url,
      };
      axios.patch("/api/products/" + theProduct.id, params).then(response => {
        console.log(response);
        theProduct = response.data;
      });
    },
    deleteProduct: function(theProduct) {
      console.log('deleting the product...');
      // make an HTTP request using axios to the destroy action of my API
      axios.delete("/api/products/" + theProduct.id).then(response => {
        console.log(response);
        // delete the product from the productss array
        // find the index of that product in the array
        var index = this.products.indexOf(theProduct);
        // remove the item based on that index
        this.products.splice(index, 1);
      });
    },
    setSortAttribute: function(attribute) {
      console.log(attribute);
      this.sortAttribute = attribute;
      if (this.sortAsc === 1) {
        this.sortAsc = -1;
      } else {
        this.sortAsc = 1;
      }
    }
  }
};
</script>