<template>
  <div class="flex w-full h-full gap-5">
    <div>
      <h1 class="text-center text-3xl">Company</h1>
      <ul class="p-10 text-xl">
        <li
          v-for="(company, index) in companies"
          :key="index"
          @click="getCategories(company)"
          class="hover:text-blue-500 hover:cursor-pointer px-2"
          :class="company === currentCompany && 'bg-blue-200 rounded-lg'"
        >
          {{ `${index + 1}: ${company}` }}
        </li>
      </ul>
    </div>
    <div v-if="categories">
      <h1 class="text-center text-3xl">Category</h1>
      <ul class="p-10 text-xl">
        <li
          v-for="(category, index) in categories"
          :key="index"
          @click="getProducts(category)"
          class="hover:text-blue-500 hover:cursor-pointer px-2"
          :class="category === currentCategory && 'bg-blue-200 rounded-lg'"
        >
          {{ `${index + 1}: ${category}` }}
        </li>
      </ul>
    </div>

    <div
      v-if="currentProducts"
      class="border border-blue-500 p-2 rounded-lg mt-10 h-[21rem]"
    >
      <ul class="text-xl max-h-[20rem] overflow-scroll scrollbar">
        <li
          v-for="(item, index) in currentProducts"
          :key="index"
          @click="selectedProduct = item"
          class="hover:text-blue-500 hover:cursor-pointer"
        >
          {{ `${index + 1}: ${item.product}` }}
        </li>
      </ul>
    </div>
    <div
      v-if="selectedProduct"
      class="flex flex-col justify-between bg-blue-200 p-10 h-[21rem] mt-10 border border-blue-500 rounded-xl"
      @click="addToBasket(selectedProduct)"
    >
      <div>
        <p>Product: {{ selectedProduct.product }}</p>
        <p>Series: {{ selectedProduct.series }}</p>
        <p>company: {{ selectedProduct.company }}</p>
        <p>Uid: {{ selectedProduct.id }}</p>
      </div>
      <div class="flex justify-end">
        <button class="bg-blue-500 text-white px-5 py-2 mt-5 rounded-xl">
          Add to basket
        </button>
      </div>
    </div>
  </div>
  <div>
    <ul class="p-10 text-xl">
      <li
        v-for="(item, index) in basket"
        :key="index"
        class="hover:text-blue-500 hover:cursor-pointer"
      >
        {{ `${index + 1}: ${item.product}` }}
        <button class="bg-blue-500 text-sm text-white px-2 py-1 mt-5 rounded-xl"> remove </button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import data from "../products.json";

const companies = ref();
const categories = ref();

const companyProducts = ref();

const currentProducts = ref();
const currentCompany = ref();
const currentCategory = ref();

const selectedProduct = ref();

const basket = ref([]);

const getCompanies = () => {
  const arr = data.map((val) => val.company);
  companies.value = [...new Set(arr)];
};

const getCategories = (id) => {
  currentCompany.value = id;

  const allProducts = data.filter((val) => val.company === id);
  // save company products
  companyProducts.value = allProducts;

  const arr = allProducts.map((val) => val.series);
  categories.value = [...new Set(arr)];
};

const getProducts = (id) => {
  currentCategory.value = id;
  const categoryProducts = companyProducts.value.filter(
    (val) => val.series === id
  );
  console.log("category products", id, currentProducts, categoryProducts);
  currentProducts.value = categoryProducts;
};

const addToBasket = (product) => {
  basket.value = [...basket.value, product];
  console.log(basket.value);
};

onMounted(() => {
  console.log("products", data);
  getCompanies();
});
</script>

<style>
.scrollbar::-webkit-scrollbar {
  @apply w-3 h-2;
}

.scrollbar::-webkit-scrollbar-track {
  @apply rounded-xl;
}

.scrollbar::-webkit-scrollbar-thumb {
  @apply bg-blue-500 rounded-xl border border-blue-200;
}

.scrollbar::-webkit-scrollbar-thumb:hover {
  background: #c0a0b9;
}
</style>
