// can add html css and js in the same file
<template>

    <div
      v-if="showingProduct"
      style="padding-left: 15px; display: flex; flex-wrap: wrap"
    >
      <div
        v-for="product in products"
        style="
          margin: 2%;
          background-color: #a3b18a;
          padding: 2%;
          border-radius: 2%;
          width: 20%;
        "
        :key="product.id"
      >
        <figure>
          <img v-bind:src="product.image" height="100px" width="100px" />
        </figure>
        <h2 v-text="product.title"></h2>
        <p v-text="product.description"></p>
        <br />
        <p>Price: AED {{ product.price }}</p>
        <br />
        <p>
          Available Inventory:
          {{ product.availableInventory - cartCount(product.id) }}
        </p>
        <br />
        <p>Location: {{ product.location }}</p>
        <br />
        <button
          class="addtocart"
          v-on:click="addCart(product)"
          v-if="canAddToCart(product)"
        >
          Add to Cart
        </button>
        <!-- This button will be displayed if it is false -->
        <button class="addtocart" disabled="disabled" v-else>
          Add to Cart
        </button>
        <span v-if="product.availableInventory === cartCount(product.id)"
          >All out!</span
        >
        <span
          v-else-if="product.availableInventory - cartCount(product.id) < 10"
        >
          Only
          {{ product.availableInventory - cartCount(product.id) }} left! </span
        ><span v-else>Buy now!</span>
      </div>
    </div>

</template>

<script>
export default {
  /* eslint-disable */
  name: "ProductsList",
  //props are being added here 
  props: ["products", "cart", "showingProduct"],    
  data() {
    return {};
  },
  methods: {
    canAddToCart: function (product) {
      return product.availableInventory > this.cartCount(product.id);
    },
    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
    addCart(product) {
      //emit is used for triggering the prop
      this.$emit("addProduct", product);
    },
  },
  computed: {
    cartItemCount: function () {
      return this.cart.length || " ";
    },
  },
};
</script>