<template>
  <div>
    <div class="header">
      <h1 class="header-title">
        <template v-if="isMobileAddMode">
          Добавление товара
        </template>
        <template v-else>
          Список продуктов
        </template>
      </h1>
      <div
        @blur="customSelect.isOpen = false"
        class="custom-select"
        tabindex="0"
      >
        <div
          v-if="windowWidth > 600"
          @click="customSelect.isOpen = !customSelect.isOpen"
          class="border form-field form-field--select"
        >
          {{ customSelect.selected.text }}
        </div>
        <div
          v-else
          class="custom-select-mobile-filters"
        >
          <template v-if="isMobileAddMode">
            <button
              @click="closeMobileAddMode"
              class="mobile-icon-button mobile-icon-button--close"
            ></button>
          </template>
          <template v-else>
            <button
              @click="mobile.addMode = !mobile.addMode"
              class="mobile-icon-button mobile-icon-button--add"
            ></button>
            <button
              @click="customSelect.isOpen = !customSelect.isOpen"
              class="mobile-icon-button mobile-icon-button--filter"
            ></button>
          </template>
        </div>
        <div
          v-if="customSelect.isOpen"
          class="custom-select-options"
          :class="{ 'custom-select-options--mobile': windowWidth <= 600 }"
        >
          <div
            v-for="(option, index) of customSelect.options"
            :key="index"
            class="custom-select-option"
          >
            <span
              v-if="customSelect.selected !== option"
              @click="
                customSelect.selected = option;
                customSelect.isOpen = false;
              "
            >
              {{ option.text }}
            </span>
            <span
              class="disabled"
              v-else
            >
              {{ option.text }}
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="main">
      <div
        v-if="windowWidth >= 600 || isMobileAddMode"
        class="form border"
      >
        <div class="form-item">
          <label
            class="required"
            for="productName"
          >
            Наименование товара
          </label>
          <input
            v-model.trim="form.productName"
            @blur="$v.form.productName.$touch()"
            id="productName"
            class="border form-field"
            :class="{ 'form-field--error': $v.form.productName.$error }"
            type="text"
            placeholder="Введите наименование товара"
          />
          <span
            v-if="$v.form.productName.$error && !$v.form.productName.required"
            class="error"
          >
            Поле является обязательным
          </span>
        </div>
        <div class="form-item">
          <label for="productDescription">
            Описание товара
          </label>
          <textarea
            v-model.trim="form.productDescription"
            id="productDescription"
            class="border form-field"
            name="description"
            rows="6"
            placeholder="Введите описание товара"
          ></textarea>
        </div>
        <div class="form-item">
          <label
            class="required"
            for="productImgLink"
          >
            Ссылка на изображение товара
          </label>
          <input
            v-model.trim="form.productImgLink"
            @blur="$v.form.productImgLink.$touch()"
            class="border form-field"
            :class="{ 'form-field--error': $v.form.productImgLink.$error }"
            id="productImgLink"
            type="text"
            placeholder="Введите ссылку"
          />
          <span
            v-if="$v.form.productImgLink.$error && !$v.form.productImgLink.required"
            class="error"
          >
            Поле является обязательным
          </span>
        </div>
        <div class="form-item">
          <label
            class="required"
            for="productPrice"
          >
            Цена товара
          </label>
          <input
            v-model.trim="price"
            @blur="$v.form.productPrice.$touch()"
            ref="productPrice"
            class="border form-field"
            :class="{ 'form-field--error': $v.form.productPrice.$error }"
            id="productPrice"
            type="text"
            placeholder="Введите цену"
          />
          <span
            v-if="$v.form.productPrice.$error && !$v.form.productPrice.required"
            class="error"
          >
            Поле является обязательным
          </span>
        </div>
        <button
          @click="createCard"
          class="button"
          :class="$v.form.$invalid ? 'button--disabled' : 'button--success'"
        >
          Добавить товар
        </button>
      </div>
      <template v-if="windowWidth >= 600 || !isMobileAddMode">
        <transition-group
          v-if="cardsLoading || sortedCards.length"
          class="cards"
          name="list"
          tag="div"
        >
          <template v-if="!cardsLoading">
            <div
              class="cards-item"
              v-for="(card, index) in sortedCards"
              :key="card.productId"
            >
              <div
                @mouseover="isCardHover = true; hoveredProductCard = card;"
                @mouseleave="isCardHover = false; hoveredProductCard = null;"
                class="card border"
              >
                <div class="img-wrapper">
                  <img
                    :src="card.productImgLink"
                    :alt="card.productName"
                  >
                </div>
                <div class="card-content">
                  <h5 class="card-title">{{ card.productName }}</h5>
                  <p class="card-description">{{ card.productDescription }}</p>
                  <span class="card-price">{{ card.productPrice }} руб.</span>
                </div>
                <div
                  v-if="isCardHover && card === hoveredProductCard"
                  class="card-button"
                >
                  <button
                    @click="deleteCard(index)"
                  ></button>
                </div>
              </div>
            </div>
          </template>
          <template v-else>
            <div
              class="cards-item"
              v-for="i in 3"
              :key="i"
            >
              <SkeletonLoader class="card" />
            </div>
          </template>
        </transition-group>
        <div
          v-else
          class="cards"
        >
          <h2 class="cards-title">Добавленных товаров нет</h2>
        </div>
      </template>
    </div>
  </div>
</template>

<script>
import { required } from 'vuelidate/lib/validators';
import SkeletonLoader from '@/components/SkeletonLoader.vue';

export default {
  name: "product-list",
  components: {
    SkeletonLoader,
  },
  data() {
    return {
      cards: [],
      cardsLoading: false,
      customSelect: {
        options: [
          {
            text: "По наименованию",
            value: "name",
          },
          {
            text: "По минимальной цене",
            value: "min",
          },
          {
            text: "По максимальной цене",
            value: "max",
          },
        ],
        isOpen: false,
        selected: {
          text: "По наименованию",
          value: "name",
        },
      },
      form: {
        productName: "",
        productDescription: "",
        productImgLink: "",
        productPrice: null
      },
      isCardHover: false,
      hoveredProductCard: null,
      mobile: {
        addMode: false,
      },
      windowWidth: window.innerWidth,
    };
  },
  validations: {
    form: {
      productName: {required},
      productImgLink: {required},
      productPrice: {required},
    }
  },
  computed: {
    price: {
      get: function () {
        return this.form.productPrice;
      },
      set: function (price) {
        this.form.productPrice = this.maskedPrice(price);
      }
    },
    isMobileAddMode() {
      return this.windowWidth <= 600 && this.mobile.addMode
    },
    sortedCards() {
      switch (this.customSelect.selected.value) {
        case 'name': {
          this.sortByProperty(this.cards, 'productName');
          break;
        }
        case 'min': {
          this.sortByProperty(this.cards, 'productPrice');
          break;
        }
        case 'max': {
          this.sortByProperty(this.cards, 'productPrice');
          this.cards.reverse();
          break;
        }
      }

      return this.cards;
    }
  },
  async mounted() {
    await this.loadCards();
    this.$nextTick(() => {
      window.addEventListener('resize', this.onResize);
    });
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.onResize);
  },
  methods: {
    async loadCards() {
      this.cardsLoading = true;
      await new Promise(resolve => setTimeout(resolve, 2000));
      const cards = await this.getFromLocalStorage('cards');
      this.cards = cards ? cards : [];
      this.cardsLoading = false;
    },
    closeMobileAddMode() {
      this.form = {
        productId: null,
        productName: "",
        productDescription: "",
        productImgLink: "",
        productPrice: null
      };
      this.mobile.addMode = false;
      this.$v.form.$reset();
    },
    createCard() {
      this.$v.form.$touch();
      if (this.$v.form.$invalid) {
        return;
      }

      this.cards.push({
        productId: Date.now(),
        productName: this.form.productName,
        productDescription: this.form.productDescription,
        productImgLink: this.form.productImgLink,
        productPrice: this.maskedPrice(this.form.productPrice),
      });
      this.form = {
        productId: null,
        productName: "",
        productDescription: "",
        productImgLink: "",
        productPrice: null
      };
      this.mobile.addMode = false;
      this.setToLocalStorage('cards', this.cards);

      this.$v.form.$reset();
    },
    deleteCard(index) {
      this.cards = this.cards.filter((card, id) => id !== index);
      this.setToLocalStorage('cards', this.cards);
    },
    maskedPrice(price) {
      if (!price || isNaN(price.replace(/ /g, ''))) return '';

      return price
        .toString()
        .replace(/ /g, '')
        .split('')
        .map((num, i, arr) => {
          return (arr.length - 1 - i) % 3 === 0 && i !== arr.length - 1
            ? num + ' '
            : num
        })
        .join('');
    },
    onResize() {
      this.windowWidth = window.innerWidth;
    },
    sortByProperty(array, property) {
      if (property === 'productPrice') {
        array.sort((a, b) => {
          const first = parseFloat(a[property].replace(/ /g, ''));
          const second = parseFloat(b[property].replace(/ /g, ''));

          if (first > second) return 1;
          if (first < second) return -1;
          return 0;
        });
      } else {
        array.sort((a, b) => {
          if (a[property] > b[property]) return 1;
          if (a[property] < b[property]) return -1;
          return 0;
        });
      }
    },
    getFromLocalStorage(name) {
      return localStorage.getItem(name) ? JSON.parse(localStorage.getItem(name)) : null;
    },
    setToLocalStorage(name, value) {
      this.removeFromLocalStorage(name);
      localStorage.setItem(name, JSON.stringify(value));
    },
    removeFromLocalStorage(name) {
      localStorage.removeItem(name);
    }
  }
}
</script>

<style lang="scss" scoped>
@import 'assets/styles/helpers';
@import 'assets/styles/variables';

.border {
  background-color: $light-beige;
  border-radius: .4rem;
  box-shadow: 0 2rem 3rem rgba(0, 0, 0, 0.04), 0 6rem 10rem rgba(0, 0, 0, 0.02);
}

.button {
  display: block;
  width: 100%;
  margin-top: 2.4rem;
  padding: 1rem;
  background-color: $grey;
  border: none;
  border-radius: 1rem;
  cursor: pointer;
  font-size: 1.2rem;
  font-weight: 600;
  font-family: 'Inter', sans-serif;
  color: $light-grey;
  text-align: center;

  &--success {
    transition: background-color .2s linear;
    background-color: $green;
    color: #FFF;

    &:hover {
      background-color: rgba($green, .8);
    }

    &:active {
      outline: 1px solid darken($green, 10%);
      background-color: $green;
    }
  }

  &--disabled {
    cursor: not-allowed;
  }
}

.error {
  display: inline-block;
  margin-top: .4rem;
  font-size: .8rem;
  color: $red;
}

/* Header */
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-bottom: 1.6rem;
}

.header-title {
  font-size: 2.8rem;
  font-weight: 600;

  @media (max-width: 600px) {
    font-size: 2rem;
  }
}

/* Custom header select */
.custom-select {
  position: relative;
  max-width: 20rem;
  outline: none;
}

.custom-select-mobile-filters {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.custom-select-options {
  position: absolute;
  left: 0;
  right: 0;
  z-index: 10;
  overflow: hidden;
  border-radius: 0 0 .4rem .4rem;
  box-shadow: 0 .2rem .5rem rgba(0, 0, 0, 0.1);

  &--mobile {
    left: -10.5rem;
    top: calc(100% + 1rem);
  }
}

.custom-select-option {
  cursor: pointer;
  user-select: none;
  font-size: 1.2rem;
  font-family: inherit;
  line-height: 1.5rem;

  span {
    display: block;
    padding: 1rem 1.6rem;
    background-color: $light-beige;
    color: $dark-grey;
  }

  span:not(.disabled) {
    transition: color .2s linear, background-color .1s linear;

    &:hover {
      background-color: $light-grey;
      color: $light-beige;
    }
  }

  span.disabled {
    background-color: lighten(#000, 90%);
  }
}

/* Main content */
.main {
  display: flex;
  align-items: flex-start;

  @media (max-width: 600px) {
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }
}

/* Cards */
.cards {
  display: flex;
  align-items: stretch;
  align-content: stretch;
  flex-direction: row;
  flex-wrap: wrap;
  width: 75.85%;

  @media (min-width: 768px) and (max-width: 1024px) {
    width: 65%;
  }

  @media (min-width: 600px) and (max-width: 768px) {
    width: 50%;
  }

  @media (max-width: 600px) {
    width: 100%;
    margin-top: 1.6rem;
  }
}

.cards-title {
  padding-left: 1.6rem;
  font-size: 2.4rem;
  font-weight: 600;
}

.cards-item {
  transition: transform .2s linear;
  width: 33.3333%;
  padding: 0 0 1.6rem 1.6rem;

  @media (min-width: 768px) and (max-width: 1024px) {
    width: 50%;
  }

  @media (min-width: 320px) and (max-width: 768px) {
    width: 100%;
  }

  @media (min-width: 425px) and (max-width: 600px) {
    margin-left: auto;
    margin-right: auto;
    width: 80%;
  }

  @media (max-width: 425px) {
    width: 90%;
  }
}

/* Card */
.card {
  transition: transform .2s linear;
  position: relative;
  height: 100%;
  cursor: pointer;

  &:hover {
    transform: scale(1.03);
  }
}

.card-content {
  padding: 1.6rem 1.6rem 2.4rem 1.6rem;
}

.card-title {
  padding-bottom: 1.6rem;
  font-size: 2rem;
  font-weight: 600;
}

.card-description {
  margin-bottom: 3.2rem;
  padding-right: .6rem;
  max-height: 10rem;
  overflow: auto;
}

.card-price {
  font-size: 2.4rem;
  font-weight: 600;
}

.card-button {
  position: absolute;
  top: -.8rem;
  right: -.8rem;

  button {
    transition: background-color .2s linear;
    position: relative;
    width: 3.2rem;
    height: 3.2rem;
    border-radius: 1rem;
    background-color: $red;

    &::after {
      content: '';
      position: absolute;
      top: 0;
      bottom: 0;
      right: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: url('../../assets/img/bucket.svg') center / 1.6rem no-repeat;
    }

    &:hover {
      background-color: darken($red, 10%);
    }
  }
}

.img-wrapper {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 20rem;

  img {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    height: 100%;
    width: 100%;
    object-fit: cover;
  }
}

/* Form */
.form {
  padding: 2.4rem;
  width: 24.15%;
  height: max-content;

  @media (min-width: 768px) and (max-width: 1024px) {
    width: 35%;
  }

  @media (min-width: 600px) and (max-width: 768px) {
    width: 50%;
  }

  @media (max-width: 600px) {
    width: 100%;
  }
}

.form-item {
  display: flex;
  flex-direction: column;

  & + & {
    margin-top: 1.6rem;
  }

  label {
    position: relative;
    display: inline-block;
    margin-bottom: .4rem;
    width: max-content;
    font-size: 1rem;
    font-family: inherit;
    line-height: 1.3rem;
    letter-spacing: -0.02em;
    color: $dark-blue;

    &.required {
      &::after {
        content: '';
        position: absolute;
        top: 0;
        right: -.5rem;
        width: .4rem;
        height: .4rem;
        border-radius: 50%;
        background-color: $red;
      }
    }
  }
}

.form-field {
  transition: box-shadow .2s linear;
  width: 100%;
  border: 1px solid transparent;
  border-radius: .4rem;
  box-shadow: 0 .2rem .5rem rgba(0, 0, 0, 0.1);
  outline: none;
  padding: 1rem 1.6rem;
  resize: none;
  font-size: 1.2rem;
  font-family: inherit;
  line-height: 1.5rem;
  color: $dark-grey;

  &:hover {
    box-shadow: 0 .2rem .5rem rgba($dark-blue, 0.7);
  }

  &:focus {
    box-shadow: 0 .2rem .5rem $light-grey;
  }

  &::placeholder {
    color: $light-grey;
  }

  &--select {
    width: 100%;
    position: relative;
    padding-right: 2.8rem;
    cursor: pointer;
    user-select: none;
    color: $light-grey;

    &::after {
      content: '';
      position: absolute;
      top: 0;
      bottom: 0;
      right: 1.4rem;
      display: inline-block;
      margin: auto 0;
      width: .8rem;
      height: .6rem;
      background: url('../../assets/img/arrow-down.svg') center / 100% no-repeat;
    }
  }

  &--error {
    border: 1px solid $red;
    box-shadow: none;

    &:hover {
      box-shadow: 0 .2rem .5rem rgba($red, 0.7);
    }
  }
}

/* Mobiles header buttons */
.mobile-icon-button {
  width: 1.6rem;
  height: 1.6rem;
  margin: 0 .5rem;
  padding: 0;

  &--add {
    background: url('../../assets/img/add.svg') center / 1.6rem no-repeat;
  }

  &--close {
    background: url('../../assets/img/close.svg') center / 1.6rem no-repeat;
  }

  &--filter {
    background: url('../../assets/img/filter.svg') center / 1.6rem no-repeat;
  }
}

/* Animation */
.list-enter-active, .list-leave-active {
  transition: transform .4s linear, opacity .2s linear;
}

.list-enter, .list-leave-to {
  opacity: 0;
  transform: scale(0);
}

.list-enter-to,
.list-leave {
  opacity: 1;
  transform: scale(1);
}
</style>
