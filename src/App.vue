<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Login</router-link> |
      <button id="logout" @click=logoutUser()>Logout</button> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
  </div>
</template>

<script>

export default {
    methods: {
      logoutUser() {
          const url = "http://localhost:8080/user/logout";
          fetch(url, { method: 'POST'});
          this.$router.push('/');
      }
    }
}
</script>

<style>
#app {
  font-family: 'Roboto' sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav a {
  font-family: 'Roboto';
  color: lightgrey;
  text-decoration: none;
}

#nav a.router-link-exact-active {
  color:gray;
}

#logout {
  border: 0;
  font-family: 'Roboto';
  color: lightgray;
  font-size: 16px;
  background: none;
  cursor: pointer;
  outline: 0;
}

</style>
