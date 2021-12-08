<template>
  <div class="loader">
    <div class="loader-img"></div>
    <div class="loader-content">
      <span class="loader-button"></span>
    </div>
  </div>
</template>

<style lang="scss" scoped>
@import 'assets/styles/variables';

@keyframes gradient {
  from {
    transform: translateX(-100%);
  }
  to {
    transform: translateX(100%);
  }
}

.loader {
  transition: transform .2s linear;
  position: relative;
  height: 30rem;
  background-color: $light-beige;
  border-radius: .4rem;
  box-shadow: 0 2rem 3rem rgba(0, 0, 0, 0.04), 0 6rem 10rem rgba(0, 0, 0, 0.02);
}

.loader-img {
  position: relative;
  overflow: hidden;
  width: 100%;
  height: 20rem;
  background-color: $grey;

  &::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, hsla(0, 0%, 100%, 0), hsla(0, 0%, 100%, .3), hsla(0, 0%, 100%, 0));
    animation: loading 1.5s infinite;
    animation-name: gradient;
  }
}

.loader-content {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 10rem;
  padding: 1.6rem;
}

.loader-button {
  position: relative;
  overflow: hidden;
  display: block;
  width: 100%;
  height: 3rem;
  background-color: $grey;
  border-radius: 1rem;

  &::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    z-index: 1;
    width: 100%;
    height: 100%;
    background: linear-gradient(90deg, hsla(0, 0%, 100%, 0), hsla(0, 0%, 100%, .3), hsla(0, 0%, 100%, 0));
    animation: loading 1.5s infinite;
    animation-name: gradient;
  }
}
</style>
