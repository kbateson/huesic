<template>
  <div :style="styling">
    <div
      style="
        backgroundcolor: rgb(255, 255, 255);
        padding: 50, 10;
        margin: auto;
        width: 50%;
      "
    >
      <div>
        <span>{{ songTitle }}</span> by <span>{{ artist }}</span><br />
        <iframe
          :src="previewUrl"
          width="300"
          height="80"
          frameborder="0"
          allowtransparency="true"
          allow="encrypted-media"
        ></iframe>
      </div>
      <div>
        <button v-on:click="getSong()">New Song</button>
      </div>
      <div>
        {{ errorText }}
      </div>
      <ColorSlider ref="colorSlider" @colorChange="handleColorChange" />
      <button v-on:click="updateSong">Update</button>
    </div>
  </div>
</template>

<script>
import Axios from "axios";
import ColorSlider from "./ColorSlider.vue";

export default {
  components: {
    ColorSlider,
  },
  data() {
    return {
      artist: "Artist",
      songTitle: "Song",
      previewUrl: "",
      styling: {
        backgroundColor: "#ffffff",
        padding: "100px",
        height: "100%",
      },
      errorText: "",
      avgColor: {
        red: [],
        green: [],
        blue: []
      },
      yourColor: {
        red: 0,
        green: 0,
        blue: 0
      }
    };
  },
  methods: {
    async getSong() {
      const token = localStorage.getItem("user-token");
      const randomLetter = String.fromCharCode(
        65 + Math.floor(Math.random() * 26)
      );
      // const randomOffset = Math.floor(Math.random() * 2000);
      this.errorText = "";
      
      const request = await Axios.get(
        `https://api.spotify.com/v1/search?q=${randomLetter}&type=track&limit=1&market=US`,
        {
          headers: {
            authorization: `Bearer ${token}`,
          },
        }
      ).catch((err) => {
        console.log(err.response);
        this.errorText = err.response
          ? err.response.data.error.message
          : "An unexpected error occured.";
      });

      if (!request) return;

      const trackInfo = request.data.tracks.items[0];

      const color = Math.floor(Math.random() * 16777215)
        .toString(16)
        .padStart(6, "0");
      
      const red = parseInt(color.substring(0, 2), 16);
      const green = parseInt(color.substring(2, 4), 16);
      const blue = parseInt(color.substring(4, 6), 16);

      this.$refs.colorSlider.colorChange({ 
        red,
        green,
        blue
      });

      this.yourColor = { 
        red,
        green,
        blue
      }

      this.artist = trackInfo.artists[0].name;
      this.songTitle = trackInfo.name;
      this.previewUrl = `https://open.spotify.com/embed/track/${trackInfo.id}`;
      this.styling.backgroundColor = `#${color}`;
    },
    handleColorChange(event) {
      this.yourColor = { 
        red: event.red,
        green: event.green,
        blue: event.blue
      }

      this.styling.backgroundColor = `#${this.convertRGBtoHex(event.red, event.green, event.blue)}`;
    },
    updateSong() {
      // call to aws get current color
      this.avgColor.red.push(this.yourColor.red);
      this.avgColor.green.push(this.yourColor.green);
      this.avgColor.blue.push(this.yourColor.blue);

      const avgRed = Math.floor(this.avgColor.red.reduce((p, c) => (c + p)) / this.avgColor.red.length);
      const avgGreen = Math.floor(this.avgColor.green.reduce((p, c) => (c + p)) / this.avgColor.green.length);
      const avgBlue = Math.floor(this.avgColor.blue.reduce((p, c) => (c + p)) / this.avgColor.blue.length);

      const color = this.convertRGBtoHex(avgRed, avgGreen, avgBlue);
      console.log(color);

      // add rgb values to array and push to aws
      this.styling.backgroundColor = `#${color}`;
    },
    convertRGBtoHex(r, g, b) {
      const red = (+r).toString(16);
      const green = (+g).toString(16);
      const blue = (+b).toString(16);
      const color = 
        `${red.padStart(2, 0)}${green.padStart(2, 0)}${blue.padStart(2, 0)}`;
      return color;
    }
  },
  mounted: function () {
    this.getSong();
  },
};
</script>