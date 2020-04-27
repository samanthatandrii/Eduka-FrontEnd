<template>
  <div id="app">
    <div id="nav">
      <img src="@/assets/eduka.png" />
      <router-link to="/">Home</router-link>
      <router-link to="/test">Test</router-link>
    </div>
    <router-view />
  </div>
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
}
body {
  background-color: #f5f7f9;
  padding: 0;
}

#app {
  @import url("https://fonts.googleapis.com/css2?family=Open+Sans:wght@300;400;600&display=swap");
  font-family: "Open Sans", sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}

#nav {
  background-color: white;
  padding: 1rem 3%;
  position: fixed;
  height: 2.5rem;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  width: 100%;
  -webkit-box-shadow: 0px 7px 10px -3px rgba(179, 179, 179, 0.9);
  -moz-box-shadow: 0px 7px 10px -3px rgba(179, 179, 179, 0.9);
  box-shadow: 0px 7px 10px -3px rgba(179, 179, 179, 0.9);
  margin-bottom: 1rem;

  a {
    color: #2c3e50;
    margin-right: 1rem;
    text-decoration: none;

    &.router-link-exact-active {
      color: #e5792e;
    }
  }

  img {
    width: 8rem;
    margin-right: 2rem;
  }
}
</style>
