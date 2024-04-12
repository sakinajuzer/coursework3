<template>
  <div v-if="showingProducts == false">
    <div v-if="orderPlaced == false" class="cart-items">
      <div
        v-for="product in cartProducts"
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
        <p>Quantity: {{ cartCount(product.id) }}</p>
        <button class="addtocart" v-on:click="removeProduct(product.id)">
          Remove One
        </button>
      </div>
    </div>
    <div
      class="checkout"
      style="
        margin: 2%;
        background-color: #a3b18a;
        padding: 2%;
        border-radius: 2%;
        width: 80%;
      "
      v-if="orderPlaced == false"
    >
      <h2 style="text-align: center">Checkout</h2>
      <p>
        <strong>First Name: </strong>
        <!-- this input field is bound to firstname in the order object -->
        <input v-model.trim="order.firstName" type="text" />
      </p>
      <p>
        <strong>Last Name: </strong>
        <!-- this input field is bound to lastname in the order object -->
        <input v-model.trim="order.lastName" type="text" />
      </p>
      <p>
        <strong>Number: </strong>
        <!-- this input field is bound to lastname in the order object -->
        <input v-model.trim="order.number" type="number" />
      </p>
      <button
        class="addtocart"
        v-if="NameandPhoneValid"
        v-on:click="placeOrder"
        style="justify-content: center"
      >
        Place Order
      </button>
      <button v-else disabled="disabled" style="justify-content: center">
        Place Order
      </button>
    </div>
    <div v-if="orderPlaced">
      <h2>Order Information</h2>
      <p>First Name: {{ order.firstName }}</p>
      <p>Last Name: {{ order.lastName }}</p>
      <p>Number: {{ order.number }}</p>
      <p>Product Ordered: {{productsOrdered}}</p>
      <button class="addtocart" v-on:click="goBack">Go back</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Form-Component",
  //props are being added here
  props: ["cart", "products", "showingProducts"],
  data() {
    return {
      order: {
        firstName: "",
        lastName: "",
        number: "",
      },
      orderPlaced: false,
      productsOrdered: ''
    };
  },
  methods: {
    placeOrder() {
        this.cartProducts.forEach(element => {
                    this.productsOrdered += (element.title + " x " + this.cartCount(element.id) + " ");
                });
      this.orderPlaced = true;
    },
    removeProduct(index) {
      //emit is used to trigger
      this.$emit("cartRemove", index);
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
    goBack(){
        this.$emit('goingBack')
    }
  },
  computed: {
    cartProducts() {
      let productsArray = this.products.slice(0);
      let cartProducts = [];
      for (let i = 0; i < productsArray.length; i++) {
        for (let j = 0; j < this.cart.length; j++) {
          if (productsArray[i].id == this.cart[j]) {
            cartProducts.push(productsArray[i]);
            break;
          } else {
            continue;
          }
        }
      }
      return cartProducts;
    },
    NameandPhoneValid: function () {
      return (
        /^[A-Za-z\s]+$/.test(this.order.firstName) &&
        /^[A-Za-z\s]+$/.test(this.order.lastName) &&
        /^[0-9]+$/.test(this.order.number)
      );
    },
  },
};
</script>