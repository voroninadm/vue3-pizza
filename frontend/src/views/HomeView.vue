<template>
  <main class="content">
    <form action="#" method="post">
      <div class="content__wrapper">
        <h1 class="title title--big">Конструктор пиццы</h1>

        <dough-selector v-model="doughId" :doughs="dataStore.doughs" />

        <size-selector v-model="sizeId" :sizes="dataStore.sizes" />

        <div class="content__ingredients">
          <div class="sheet">
            <h2 class="title title--small sheet__title">
              Выберите ингредиенты
            </h2>
            <div class="sheet__content ingredients">
              <sauce-selector v-model="sauceId" :sauces="dataStore.sauces" />

              <ingredients-selector
                :ingredients="ingredients"
                :values="pizza.ingredients"
                @update="updateIngredientAmount"
              />
            </div>
          </div>
        </div>

        <div class="content__pizza">
          <label class="input">
            <span class="visually-hidden">Название пиццы</span>
            <input
              v-model="pizza.name"
              type="text"
              name="pizza_name"
              placeholder="Введите название пиццы"
            />
          </label>

          <pizza-constructor
            :dough="pizzaStore.dough.value"
            :sauce="pizzaStore.sauce.value"
            :ingredients="pizza.ingredients"
            @drop="addIngredient"
          />

          <div class="content__result">
            <p>Итого: 0 ₽</p>
            <button type="button" class="button" disabled>Готовьте!</button>
          </div>
        </div>
      </div>
    </form>
  </main>
</template>

<script setup>
import { reactive, computed } from "vue";

// components
import DoughSelector from "../modules/constructor/DoughSelector.vue";
import SizeSelector from "../modules/constructor/SizeSelector.vue";
import SauceSelector from "../modules/constructor/SauceSelector.vue";
import IngredientsSelector from "../modules/constructor/IngredientsSelector.vue";
import PizzaConstructor from "../modules/constructor/PizzaConstructor.vue";

import {
  normalizeDough,
  normalizeIngredients,
  normalizeSauces,
  normalizeSize,
} from "@/common/helpers/normalize";

//mocks
import doughsJSON from "../mocks/dough.json";
import ingredientsJSON from "../mocks/ingredients.json";
import saucesJSON from "../mocks/sauces.json";
import sizesJSON from "../mocks/sizes.json";

// mocks with data value
const doughs = doughsJSON.map(normalizeDough);
const ingredients = ingredientsJSON.map(normalizeIngredients);
const sauces = saucesJSON.map(normalizeSauces);
const sizes = sizesJSON.map(normalizeSize);

import { useDataStore } from "@/stores/data";
import { usePizzaStore } from "@/stores/pizza";
import { useCartStore } from "@/stores/cart";

const dataStore = useDataStore();
const pizzaStore = usePizzaStore();
const cartStore = useCartStore();

const doughId = computed({
  get() {
    return pizzaStore.doughId;
  },
  set(value) {
    pizzaStore.setDough(value);
  },
});

const sizeId = computed({
  get() {
    return pizzaStore.sizeId;
  },
  set(value) {
    pizzaStore.setSize(value);
  },
});

const sauceId = computed({
  get() {
    return pizzaStore.sauceId;
  },
  set(value) {
    pizzaStore.setSauce(value);
  },
});

const pizza = reactive({
  name: "",
  dough: doughId,
  size: sizes[1].value,
  sauce: sauces[0].value,
  ingredients: ingredients.reduce((prevIngredient, ingredient) => {
    prevIngredient[ingredient.value] = 0;
    return prevIngredient;
  }, {}),
});

const addIngredient = (ingredient) => {
  pizza.ingredients[ingredient]++;
};

const updateIngredientAmount = (ingredient, count) => {
  pizza.ingredients[ingredient] = count;
};
</script>

<style lang="scss">
@import "@/assets/scss/ds-system/ds.scss";
@import "@/assets/scss/mixins/mixins.scss";

.content__ingredients {
  width: 527px;
  margin-top: 15px;
  margin-right: auto;
  margin-bottom: 15px;
}

.content__pizza {
  width: 373px;
  margin-top: 15px;
  margin-bottom: 15px;
}

.content__result {
  display: flex;
  align-items: center;
  justify-content: center;

  margin-top: 25px;

  p {
    @include b-s24-h28;

    margin: 0;
  }

  button {
    margin-left: 12px;
    padding: 16px 45px;
  }
}
</style>
