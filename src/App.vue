<template>
  <div id="app">
    <img src="./assets/huesic.png" />
    <HelloWorld msg="Welcome to Huesic" />
    <Song />
  </div>
</template>

<script>
import Axios from "axios";
import HelloWorld from "./components/HelloWorld";
import Song from "./components/Song";

export default {
  name: "App",

  components: {
    HelloWorld,
    Song,
  },

  data: () => ({
    //
  }),

  mounted: async function () {
    const config = {
      method: "post",
      url: "https://accounts.spotify.com/api/token",
      headers: {
        Authorization:
          "Basic ## insert auth token here ##",
        "Content-Type": "application/x-www-form-urlencoded",
      },
      data: "grant_type=client_credentials",
    };

    const tokenRequest = await Axios(config).catch((err) => {
      console.log(err);
    });
    console.log(tokenRequest.data.access_token);
    localStorage.setItem('user-token', tokenRequest.data.access_token);
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

