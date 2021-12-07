<template>
  <div>
    <div class="header">
      <h1 class="header-title">Добавление товара</h1>
      <div
        @blur="customSelect.isOpen = false"
        class="custom-select"
        tabindex="0"
      >
        <div
          @click="customSelect.isOpen = !customSelect.isOpen "
          class="border form-field form-field--select"
        >
          {{ customSelect.selected }}
        </div>
        <div
          v-if="customSelect.isOpen"
          class="custom-select-options"
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
              {{ option }}
            </span>
            <span
              class="disabled"
              v-else
            >
              {{ option }}
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="main">
      <div class="form border">
        <div class="form-item">
          <label
            class="required"
            for="productName"
          >
            Наименование товара
          </label>
          <input
            id="productName"
            class="border form-field"
            type="text"
            placeholder="Введите наименование товара"
          />
        </div>
        <div class="form-item">
          <label for="productDescription">
            Описание товара
          </label>
          <textarea
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
            class="border form-field"
            id="productImgLink"
            type="text"
            placeholder="Введите ссылку"
          />
        </div>
        <div class="form-item">
          <label
            class="required"
            for="productPrice"
          >
            Цена товара
          </label>
          <input
            class="border form-field"
            id="productPrice"
            type="text"
            placeholder="Введите цену"
          />
        </div>
        <button
          @click.stop
          class="button button--success"
        >
          Добавить товар
        </button>
      </div>
      <div class="cards">
        <div
          class="card border"
          @mouseover="isCardHover = true"
          @mouseleave="isCardHover = false"
        >
          <div class="img-wrapper">
            <img
              src=""
              alt="product"
            >
          </div>
          <div class="card-content">
            <h5 class="card-title">Наименование товара</h5>
            <p class="card-description">
              Довольно-таки интересное описание товара в несколько строк. Довольно-таки интересное описание товара в несколько строк
            </p>
            <span class="card-price">10 000 руб.</span>
          </div>
          <div
            v-if="isCardHover"
            class="card-button"
          >
            <button></button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "product-list",
  data() {
    return {
      customSelect: {
        options: [
          "По наименованию",
          "По минимальной цене",
          "По максимальной цене",
        ],
        isOpen: false,
        selected: "По наименованию",
      },
      isCardHover: false,
    };
  },
}
</script>

<style lang="scss" scoped>
@import 'assets/styles/variables';

button {
  border: none;
  outline: none;
  background: none;
  cursor: pointer;
}

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

.header {
  display: flex;
  justify-content: space-between;
  padding-bottom: 1.6rem;
}

.header-title {
  font-size: 2.8rem;
  font-weight: 600;
}

.custom-select {
  position: relative;
  max-width: 20rem;
  outline: none;
}

.custom-select-options {
  position: absolute;
  left: 0;
  right: 0;
  z-index: 1;
  overflow: hidden;
  border-radius: 0 0 .4rem .4rem;
  box-shadow: 0 .2rem .5rem rgba(0, 0, 0, 0.1);
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
    background-color: rgba(#000, .1);
  }
}

.main {
  display: flex;
}

.cards {
  align-items: flex-start;
  display: flex;
  flex-basis: 75.85%;
  flex-direction: row;
  flex-wrap: wrap;
}

.card {
  transition: transform .2s linear;
  position: relative;
  margin: 0 0 1.6rem 1.6rem;
  max-width: 31.8%;
  width: 100%;
  cursor: pointer;

  &:hover {
    transform: scale(1.05);
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
  padding-bottom: 3.2rem;
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
    position: relative;
    width: 3.2rem;
    height: 3.2rem;
    border-radius: 1rem;
    background-color: $red;

    &::after {
      content: '';
      transition: background-size .2s linear;
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
      &::after {
        background-size: 2.3rem;
      }
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

.form {
  padding: 2.4rem;
  flex-basis: 24.15%;
  height: max-content;
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
  border: none;
  box-shadow: 0 .2rem .5rem rgba(0, 0, 0, 0.1);
  outline: none;
  padding: 1rem 1.6rem;
  resize: none;
  font-size: 1.2rem;
  font-family: inherit;
  line-height: 1.5rem;
  color: $dark-grey;

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

  &::placeholder {
    color: $light-grey;
  }

  &:hover {
    box-shadow: 0 .2rem .5rem rgba($dark-blue, 0.7);
  }

  &:focus {
    box-shadow: 0 .2rem .5rem $light-grey;
  }
}
</style>
