<template>
  <div>
    <div class="navbar">
      <div class="name">
        <p>After School Fun</p>
      </div>
      <div class="search-bar">
        <span>
          <input
            type="text"
            placeholder="Search"
            v-model="search"
            @input="searching"
          />
        </span>
      </div>
      <div class="carticon" v-on:click="showCheckout" v-if="cartItemCount > 0">
        <span>{{ cartItemCount }}</span>
        <i class="fas fa-shopping-cart"></i>
      </div>
      <div class="carticon" v-else>
        <i class="fas fa-shopping-cart"></i>
      </div>
    </div>
    <div
      style="display: flex; height: 100vh; width: 100%; background-color: black"
    >
      <div
        class="filter"
        style="width: 20%; height: 100%; background-color: #dad7ca; overflow: auto; padding: 2%;"
      >
        <div style="margin: 2%; background-color: #a3b18a; padding: 2%; border-radius: 2%; width: 100%;">
          <p style="text-align: center">FITLER</p>
          <p>Sort BY:</p>
          <label>
            <input type="radio" value="price" v-model="sort" />Price
            <br />
            <input type="radio" value="location" v-model="sort" />Location
            <br />
            <input
              type="radio"
              value="availability"
              v-model="sort"
            />Availability
            <br />
            <input type="radio" value="title" v-model="sort" />Title
          </label>
          <label>
            <p>Sort order</p>
            <input
              type="radio"
              value="ascending"
              v-model="sortOrder"
            />Ascending
            <br />
            <input
              type="radio"
              value="descending"
              v-model="sortOrder"
            />Desending
          </label>
        </div>
      </div>
      <div
        class="products"
        style=" width: 80%;height: 100%;background-color: #dad7ca; overflow: auto; padding: 2%;"
      >
        <div>
          <header>
            <h1
              v-text="sitename"
              style="text-align: center; padding-bottom: 10px"
            ></h1>
          </header>
          <main>
            <div class="productblock" style="padding-top: 10px">
              <product-list
                v-if="search == ''"
                @addProduct="addToCart"
                :products="sortedProducts"
                :cart="cart"
                :showingProduct="showProduct"
              ></product-list>
              <product-list
                v-if="search != ''"
                @addProduct="addToCart"
                :products="searchingProductsDisplay"
                :cart="cart"
                :showingProduct="showProduct"
              ></product-list>
              <checkout
                :cart="cart"
                :products="products"
                :showingProducts="showProduct"
                @cartRemove="removeProduct"
                @goingBack="emptyCart"
              ></checkout>
            </div>
          </main>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable */
//removes eslint errors
import productList from "./components/ProductList.vue";
import checkout from "./components/Checkout.vue";

export default {
  name: "App",
  components: {
    productList,
    checkout,
  },
  data() {
    return {
      orderPlaced: false,
      showProduct: true,
      sitename: "Classes",
      sort: "",
      sortOrder: "ascending",
      search: "",
      order: {
        firstName: "",
        lastName: "",
        number: "",
      },
      products: [],
      states: {
        AL: "Alabama",
        AR: "Arizona",
        CA: "California",
        NV: "Nevada",
      },
      cart: [],
      searchingProductsDisplay: [],
    };
  },
  //fetches data from mongodb
  created: function () {
    console.log("taking data from server");
    let self = this;
    fetch("http://localhost:3000/collection/products").then(function (
      response
    ) {
      response.json().then(function (json) {
        self.products = json;
        console.log(json);
      });
    });
  },
  methods: {
    showCheckout: function () {
      this.showProduct = this.showProduct ? false : true;
      this.sitename = this.showProduct ? "Courses" : "Cart";
    },
    addToCart: function (product) {
      console.log(product.image);
      this.cart.push(product.id);
    },
    removeProduct(id) {
      let found = false;
      function rearrangingCart(cartItem) {
        if (found == false) {
          if (cartItem == id) {
            found = true;
          } else {
            return cartItem;
          }
        } else {
          return cartItem;
        }
      }
      this.cart = this.cart.filter(rearrangingCart);
    },
    emptyCart() {
      // Clear the cart
      this.cart.splice(0, this.cart.length);

      location.reload();
    },
    searching: function () {
      const url = `http://localhost:3000/collection/classes/search/${this.search}`;

      // Fetch data from the server based on the search query
      fetch(url)
        .then((response) => response.json())
        .then((data) => {
          this.searchingProductsDisplay = data;
          console.log(this.searchingProductsDisplay);
        })
        .catch((error) => {
          // Log any errors that occur during the search process
          console.error("Error performing search:", error.message);
        });
    },
  },
  computed: {
    cartItemCount: function () {
      return this.cart.length || " ";
    },
    sortedProducts() {
      let cartSort = this.cart;
      let productsArray = this.products.slice(0);
      function cartCount(id) {
        let count = 0;
        for (let i = 0; i < cartSort.length; i++) {
          if (cartSort[i] === id) {
            count++;
          }
        }
        return count;
      }
      if (this.sort === "price" && this.sortOrder === "ascending") {
        function compare(a, b) {
          if (a.price > b.price) return 1;

          if (a.price < b.price) return -1;
          return 0;
        }
        return productsArray.sort(compare);
      } else if (this.sort === "price" && this.sortOrder === "descending") {
        function compare(a, b) {
          if (a.price > b.price) return 1;

          if (a.price < b.price) return -1;
          return 0;
        }
        return productsArray.sort(compare).reverse();
      } else if (this.sort === "location" && this.sortOrder === "ascending") {
        function compare(a, b) {
          if (a.location > b.location) return 1;

          if (a.location < b.location) return -1;
          return 0;
        }
        return productsArray.sort(compare);
      } else if (this.sort === "location" && this.sortOrder === "descending") {
        function compare(a, b) {
          if (a.location > b.location) return 1;

          if (a.location < b.location) return -1;
          return 0;
        }
        return productsArray.sort(compare).reverse();
      } else if (
        this.sort === "availability" &&
        this.sortOrder === "ascending"
      ) {
        function compare(a, b) {
          if (
            a.availableInventory - cartCount(a.id) >
            b.availableInventory - cartCount(b.id)
          )
            return 1;

          if (
            a.availableInventory - cartCount(a.id) <
            b.availableInventory - cartCount(b.id)
          )
            return -1;
          return 0;
        }
        return productsArray.sort(compare);
      } else if (
        this.sort === "availability" &&
        this.sortOrder === "descending"
      ) {
        function compare(a, b) {
          if (
            a.availableInventory - cartCount(a.id) >
            b.availableInventory - cartCount(b.id)
          )
            return 1;

          if (
            a.availableInventory - cartCount(a.id) <
            b.availableInventory - cartCount(b.id)
          )
            return -1;
          return 0;
        }
        return productsArray.sort(compare).reverse();
      } else if (this.sort === "title" && this.sortOrder === "ascending") {
        function compare(a, b) {
          if (a.title > b.title) return 1;

          if (a.title < b.title) return -1;
          return 0;
        }
        return productsArray.sort(compare);
      } else if (this.sort === "title" && this.sortOrder === "descending") {
        function compare(a, b) {
          if (a.title > b.title) return 1;

          if (a.title < b.title) return -1;
          return 0;
        }
        return productsArray.sort(compare).reverse();
      } else {
        return productsArray;
      }
    },
  },
};
</script>

<style>
/* Reset default margin and padding for the entire page */
html,
body {
  height: 100vh;
  margin: 0%;
}

body,
h1,
p,
ul,
li {
  margin: 0;
  padding: 0;
}

/* Style the navigation bar */
.navbar {
  background-color: #dad7cd;
  color: #344e41;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px;
}

/* Style the div below the navbar */

.name {
  font-size: x-large;
  font-weight: bold;
  padding-left: 20px;
  font-family: "Lucida Sans", "Lucida Sans Regular", "Lucida Grande",
    "Lucida Sans Unicode", Geneva, Verdana, sans-serif;
}

.navbar ul {
  list-style: none;
}

.navbar li {
  display: inline;
  margin-right: 20px;
}

.navbar a {
  text-decoration: none;
  color: #fff;
}

/* Style the cart icon */
.carticon {
  font-size: 25px;
  /* Adjust the size as needed */
  color: #344e41;
  padding-right: 20px;
}

.search-bar input {
  height: 20px;
  width: 250px;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 3px;
  margin-right: 10px;
}

/* .search-bar button {
    background-color: #344e41;
    color: #dad7cd;
    border: none;
    border-radius: 3px;
    padding: 5px 10px;
    cursor: pointer;
} */

.cart-items {
  display: flex;
  flex-wrap: wrap;
}

/* CSS */
.addtocart {
  align-self: center;
  background-color: #dad7cd;
  background-image: none;
  background-position: 0 90%;
  background-repeat: repeat no-repeat;
  background-size: 4px 3px;
  border-radius: 15px 225px 255px 15px 15px 255px 225px 15px;
  border-style: solid;
  box-shadow: rgba(0, 0, 0, 0.2) 15px 28px 25px -18px;
  box-sizing: border-box;
  color: #344e41;
  cursor: pointer;
  display: inline-block;
  font-family: Neucha, sans-serif;
  font-size: 15px;
  outline: none;
  padding: 6px;
  text-decoration: none;
  transition: all 235ms ease-in-out;
  border-bottom-left-radius: 15px 255px;
  border-bottom-right-radius: 225px 15px;
  border-top-left-radius: 255px 15px;
  border-top-right-radius: 15px 225px;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
}

.addtocart:hover {
  box-shadow: rgba(0, 0, 0, 0.3) 2px 8px 8px -5px;
  transform: translate3d(0, 2px, 0);
}

.addtocart:focus {
  box-shadow: rgba(0, 0, 0, 0.3) 2px 8px 4px -6px;
}
</style>
