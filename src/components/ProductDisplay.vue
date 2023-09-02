<template>
  <div class="container" :class="containerClass">
    <div class="wrapper">
      <div v-if="isLoading" class="loader"></div>
      <div v-else>
        <div
          :class="{
            'page-men': isMenCategory,
            'page-women': isWomenCategory,
            'page-unavailable': isUnavailableCategory,
          }"
          class="product-row"
          v-if="currentProduct"
        >
          <div class="product-image">
            <img
              :src="currentProduct ? currentProduct.image : ''"
              :alt="currentProduct ? currentProduct.title : ''"
              width="300"
              height="480"
            />
          </div>
          <div class="product-info">
            <h2>{{ currentProduct ? currentProduct.title : "" }}</h2>
            <div class="rate">
              <p class="category">
                {{ currentProduct ? currentProduct.category : "" }}
              </p>
              <div class="rating" v-if="currentProduct">
                <p>{{ currentProduct.rating.rate }} / 5</p>
                <div class="circle-rating">
                  <template v-for="index in 5">
                    <svg
                      class="circle-rating"
                      width="20"
                      height="20"
                      :fill="
                        index <= currentProduct.rating.rate
                          ? ratingColor
                          : 'white'
                      "
                      :stroke="borderColor"
                      xmlns="http://www.w3.org/2000/svg"
                      viewBox="0 0 24 24"
                    >
                      <circle cx="12" cy="12" r="10" />
                    </svg>
                  </template>
                </div>
              </div>
            </div>
            <p class="description">
              {{ currentProduct ? currentProduct.description : "" }}
            </p>
            <span class="product-price"
              >$ {{ currentProduct ? currentProduct.price : "" }}</span
            >
            <div class="action">
              <button
                :class="{
                  'btn_buy-men': isMenCategory,
                  'btn_buy-women': isWomenCategory,
                  'btn_buy-unavailable': isUnavailableCategory,
                }"
              >
                Buy Now
              </button>
              <button
                @click="getNextProduct"
                :class="{
                  'btn_next-men': isMenCategory,
                  'btn_next-women': isWomenCategory,
                  'btn_next-unavailable': isUnavailableCategory,
                }"
              >
                Next Product
              </button>
            </div>
          </div>
        </div>
        <div v-else>
          <p>empty</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "../assets/pages.css";

export default {
  data() {
    return {
      currentProduct: null,
      currentIndex: 1,
      categoriesToSave: ["men's clothing", "women's clothing"],
      isLoading: false,
      ratingColor: "#002772",
      borderColor: "#002772",
    };
  },
  computed: {
    isMenCategory() {
      return (
        this.currentProduct && this.currentProduct.category === "men's clothing"
      );
    },
    isWomenCategory() {
      return (
        this.currentProduct &&
        this.currentProduct.category === "women's clothing"
      );
    },
    isUnavailableCategory() {
      return (
        this.currentProduct &&
        !this.categoriesToSave.includes(this.currentProduct.category)
      );
    },
    containerClass() {
      return {
        "container-men": this.isMenCategory,
        "container-women": this.isWomenCategory,
        "container-unavailable": this.isUnavailableCategory,
      };
    },
  },
  methods: {
    async getNextProduct() {
      if (this.currentIndex === 20) {
        this.currentIndex = 1;
      } else {
        this.currentIndex++;
      }

      this.isLoading = true;

      try {
        const response = await fetch(
          `https://fakestoreapi.com/products/${this.currentIndex}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }
        const data = await response.json();

        if (this.categoriesToSave.includes(data.category)) {
          this.currentProduct = data;
          this.borderColor = this.isMenCategory
            ? "#002772"
            : this.isWomenCategory
            ? "#720060"
            : "gray";
          this.ratingColor = this.isMenCategory
            ? "#002772"
            : this.isWomenCategory
            ? "#720060"
            : "gray";
        } else {
          this.getNextProduct();
        }

        this.isLoading = false;
      } catch (error) {
        console.error("Error fetching product:", error);
        this.isLoading = false;
      }
    },
  },
  mounted() {
    // Panggil metode getNextProduct saat komponen dimuat pertama kali
    this.getNextProduct();
  },
};
</script>
