<template>
  <div class="root">
    <p>Name: <input type="text" v-model="newProductName"></p>
    <p>Description: <input type="text" v-model="newProductDescription"></p>
    <p>Price: <input type="text" v-model="newProductPrice"></p>
    <p>Image_url: <input type="text" v-model="newProductImage_url"></p>
    <button v-on:click="makeProduct()">Add a new Product</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      newProductName: "",
      newProductDescription: "",
      newProductPrice: "",
      newProductImage_url: ""
    };
  },

  created: function() {},
  methods: {
    makeProduct: function() {
      console.log('making the new product...');
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        image_url: this.newProductImage_url
      };

      axios.post("/api/products", params).then(response =>{
        console.log(response);
        this.$router.push("/");
      });
    }
  }
};
</script>