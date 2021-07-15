<template>
  <h1>す早く <span class="headingSplit">|</span> <span class="subHeading">Mental Excersice</span></h1>

  <div id="nav">
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link> |
    <a href="https://sdwh.dev" target="_blank">sdwh.dev</a>
  </div>
  <hr />
  <router-view />
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

h1{
  margin-bottom: 0px;
}

@media (max-width: 1024px) {
  .subHeading {
    display: block;
  }
  .headingSplit{
    display: none;
  }
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;
    font-size: 1.3rem;
    margin-left: 3%;
    margin-right: 3%;
    text-decoration: none;

    &:hover {
      color: rebeccapurple;
    }

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
