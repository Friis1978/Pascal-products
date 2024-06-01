<template>
  <div class="flex w-full h-20 px-10 items-center justify-between bg-gradient-to-r from-cyan-500 to-blue-500 mb-5 shadow-xl">
    <h1 class="text-white text-4xl">Pascal products</h1>
    <button @click="navigateTo('/figma')" class="bg-white px-3 py-2 rounded-lg">
      Goto figma
    </button>
  </div>
  <div class="flex w-full h-full gap-5 text-sm px-10">
    <div>
      <h1 class="text-2xl">Company</h1>
      <ul class="mt-10">
        <li
          v-for="(company, index) in companies"
          :key="index"
          @click="getCategories(company)"
          class="hover:text-blue-500 hover:cursor-pointer px-2"
          :class="company === currentCompany && 'bg-blue-200 rounded-lg'"
        >
          {{ company }}
        </li>
      </ul>
    </div>
    <div v-if="categories">
      <h1 class="text-2xl">Category</h1>
      <ul class="mt-10">
        <li
          v-for="(category, index) in categories"
          :key="index"
          @click="getProducts(category)"
          class="hover:text-blue-500 hover:cursor-pointer px-2"
          :class="category === currentCategory && 'bg-blue-200 rounded-lg'"
        >
          {{ category }}
        </li>
      </ul>
    </div>

    <div v-if="currentProducts">
      <h1 class="text-2xl">Products</h1>
      <div class="border border-blue-500 p-2 rounded-lg mt-10 h-[21rem]">
        <ul class="max-h-[20rem] overflow-scroll scrollbar">
          <li
            v-for="(item, index) in currentProducts"
            :key="index"
            @click="selectProduct(item)"
            class="hover:text-blue-500 hover:cursor-pointer px-2"
            :class="item?.id === selectedProduct?.id && 'bg-blue-200 rounded-lg'"
          >
            {{ item.product }}
          </li>
        </ul>
      </div>
    </div>

    <div v-if="selectedProduct">
      <h1 class="text-2xl">Selected</h1>
      <div
        class="flex flex-col justify-between bg-blue-200 p-10 h-[21rem] mt-10 border border-blue-500 rounded-xl w-[25rem]"
      >
        <div>
          <p>Product: {{ selectedProduct.product }}</p>
          <p>Series: {{ selectedProduct.series }}</p>
          <p>company: {{ selectedProduct.company }}</p>
          <p>Uid: {{ selectedProduct.id }}</p>
        </div>
        <div class="flex justify-end">
          <button
            class="text-white px-5 py-2 mt-5 rounded-xl"
            :class="inBasket ? 'bg-red-500' : 'bg-blue-500'"
            @click="
              () => {
                !inBasket
                  ? addToBasket(selectedProduct)
                  : removeFromBasket(selectedProduct.id);
              }
            "
          >
            {{ inBasket ? "Remove from basket" : "Add to basket" }}
          </button>
        </div>
      </div>
    </div>
  </div>
  <div class="w-1/2 mt-5 mb-20">
    <h1 class="pl-10 text-2xl">Basket</h1>
    <ul v-if="basket?.length" class="pl-10 text-xl gap-5">
      <li
        v-for="(item, index) in basket"
        :key="index"
        class="flex justify-between items-center text-sm py-2 border-b border-gray-300 shadow-md my-1 px-2"
      >
        <p>{{ item.product }}</p>
        <button
          class="bg-blue-500  text-white px-2 py-1 rounded-xl"
          @click="removeFromBasket(item.id)"
        >
          remove
        </button>
      </li>
    </ul>
    <p v-else class="p-10"> Basket is empty</p>
  </div>
</template>

<script setup>
import data from "../products.json";

// Save basket in cookie
const savedBasket = useCookie('basket')

const companies = ref();
const categories = ref();

const companyProducts = ref();

const currentProducts = ref();
const currentCompany = ref();
const currentCategory = ref();

const selectedProduct = ref();
const inBasket = ref(false);

const basket = ref([]);

const getCompanies = () => {
  const arr = data.map((val) => val.company);
  companies.value = [...new Set(arr)];
};

const getCategories = (id) => {
  // Clear selected
  currentProducts.value = undefined;
  currentCategory.value = undefined;
  selectedProduct.value = undefined;

  currentCompany.value = id;

  const allProducts = data.filter((val) => val.company === id);
  // save company products
  companyProducts.value = allProducts;

  const arr = allProducts.map((val) => val.series);
  categories.value = [...new Set(arr)];
};

const getProducts = (id) => {
  // Clear selected
  selectedProduct.value = undefined;

  currentCategory.value = id;
  const categoryProducts = companyProducts.value.filter(
    (val) => val.series === id
  );
  currentProducts.value = categoryProducts;
};

const selectProduct = (item) => {
  selectedProduct.value = item;
  isInBasket(item.id);
};

const addToBasket = (product) => {
  basket.value = [...basket.value, product];
  selectedProduct.value = undefined;

  // save in cookie
  savedBasket.value = basket.value;
};

const removeFromBasket = (id) => {
  const newlist = basket.value.filter((val) => val.id !== id);
  basket.value = newlist;

  // save in cookie
  savedBasket.value = basket.value;

  isInBasket(id);
};

const isInBasket = (id) => {
  // find in basket
  const isPresent = basket?.value?.filter((val) => val.id === id);
  if (isPresent?.length) inBasket.value = true;
  else inBasket.value = false;
};

onMounted(() => {
  if(savedBasket.value) basket.value = savedBasket.value;
  getCompanies();
});
</script>

<style>
/* 
 Scroll should be visible allways
*/
.scrollbar::-webkit-scrollbar {
  @apply w-3 h-2;
}

.scrollbar::-webkit-scrollbar-track {
  @apply rounded-xl;
}

.scrollbar::-webkit-scrollbar-thumb {
  @apply bg-blue-500 rounded-xl border border-blue-200 hover:bg-blue-700;
}
</style>
