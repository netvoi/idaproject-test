<template>
  <div class="home">
    <h1 class="home-title">Тестовое задание Idaproject</h1>

    <nuxt-link
      class="home-link"
      to="/product-list"
    >
      Перейти на страницу с товарами
    </nuxt-link>

    <a
      v-for="link in links"
      :key="link.href"
      :href="link.href"
      class="home-link"
      target="_blank"
    >
      {{ link.text }}
    </a>
  </div>
</template>

<script>
export default {
  name: "home",
  head: {
    title: "Главная"
  },
  data() {
    return {
      links: [
        {
          href: "https://github.com/netvoi/idaproject-test",
          text: "Перейти в репозиторий"
        },
        {
          href: "https://t.me/l1ttlesparrow",
          text: "Телеграм"
        },
      ]
    }
  }
}
</script>

<style lang="scss" scoped>
@import 'assets/styles/variables';

.home {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: calc(100vh - 6.4rem);
}

.home-title {
  margin-bottom: 2rem;
  font-size: 3.6rem;
  font-weight: 600;
}

.home-link {
  position: relative;
  color: $dark-blue;
  text-decoration: none;

  & + & {
    margin-top: 1rem;
  }

  &::after {
    content: '';
    position: absolute;
    bottom: 0;
    left: 0;
    transition: width .2s linear;
    width: 0;
    height: 1px;
    background-color: $dark-blue;
  }

  &:hover {
    &::after {
      width: 100%;
    }
  }
}
</style>
