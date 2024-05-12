<template>
  <v-container>
    <!-- Barra di ricerca -->
    <v-text-field v-model="search" label="Cerca per nome" @input="filterRadios" />
    
    <!-- Lista delle radio filtrate -->
    <v-row>
      <v-col v-for="(radio, index) in filteredRadios" :key="index" cols="3">
        <v-card color="white">
          <div class="d-flex flex-column justify-space-between" style="height: 100%;">
            <div class="d-flex flex-column justify-space-between">
              <v-card-title class="text-h5 overflow-ellipsis">{{ radio.name }}</v-card-title>
              <v-avatar class="mx-auto mb-2" rounded="0" size="100">
                <v-img :src="getRadioLogo(radio)" contain></v-img>
              </v-avatar>
            </div>
            <v-card-actions class="justify-center">
              <v-btn icon @click="togglePlayback(radio)">
                <v-icon>{{ radio.isPlaying ? 'mdi mdi-stop' : 'mdi mdi-play' }}</v-icon>
              </v-btn>
              <v-btn icon @click="toggleFavorite(radio)">
                <v-icon :color="isFavorite(radio) ? 'red' : ''">mdi mdi-heart</v-icon>
              </v-btn>
            </v-card-actions>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import Hls from 'hls.js';

export default {
  name: 'HomeView',
  data() {
    return {
      radios: [],
      defaultLogo: 'https://fiverr-res.cloudinary.com/images/t_main1,q_auto,f_auto,q_auto,f_auto/gigs/295505491/original/cfbfdfff1c5e32c8ccd5e70f86c8e434a374f431/design-attractive-radio-logo.jpg', // URL dell'immagine predefinita
      audio: null,
      currentRadio: null,
      favorites: JSON.parse(localStorage.getItem('favorites') || '[]'),
      search: ''
    };
  },
  computed: {
    filteredRadios() {
      return this.radios.filter(radio => {
        return radio.name.toLowerCase().includes(this.search.toLowerCase());
      });
    }
  },
  methods: {
    getRadios() {
      fetch('https://nl1.api.radio-browser.info/json/stations/search?limit=100&countrycode=IT&hidebroken=true&order=clickcount&reverse=true')
        .then(response => response.json())
        .then(data => {
          this.radios = data.map(radio => ({
            ...radio,
            isPlaying: false
          }));
          console.log(data);
        });
    },
    getRadioLogo(radio) {
      return radio.favicon || this.defaultLogo;
    },
    playRadio(radio) {
      if (radio.url.toLowerCase().endsWith('.m3u8')) {
        this.playM3U8Radio(radio);
      } else {
        this.playNormalRadio(radio);
      }
    },
    playM3U8Radio(radio) {
      if (Hls.isSupported()) {
        const hls = new Hls();
        hls.loadSource(radio.url);
        hls.attachMedia(this.audio);
        hls.on(Hls.Events.MANIFEST_PARSED, () => {
          this.audio.play();
          radio.isPlaying = true;
          this.currentRadio = radio;
        });
      } else {
        console.error('HLS is not supported');
      }
    },
    playNormalRadio(radio) {
      if (this.audio.canPlayType('audio/mpeg')) {
        this.audio.src = radio.url;
        this.audio.play();
        radio.isPlaying = true;
        this.currentRadio = radio;
      } else {
        console.error('Audio playback is not supported');
      }
    },
    togglePlayback(radio) {
      if (this.currentRadio && this.currentRadio !== radio) {
        if (this.currentRadio.hls === '1') {
          this.audio.pause();
        } else {
          this.audio.pause();
          this.currentRadio.isPlaying = false;
        }
      }

      if (radio.isPlaying) {
        if (radio.hls === '1') {
          this.audio.pause();
        } else {
          this.audio.pause();
          radio.isPlaying = false;
        }
        this.currentRadio = null;
      } else {
        if (!this.audio) {
          this.audio = new Audio();
        }
        this.playRadio(radio);
      }
    },
    toggleFavorite(radio) {
      const index = this.favorites.findIndex(fav => fav.url === radio.url);
      if (index !== -1) {
        this.favorites.splice(index, 1);
      } else {
        this.favorites.push(radio);
      }
      localStorage.setItem('favorites', JSON.stringify(this.favorites));
    },
    isFavorite(radio) {
      return this.favorites.some(fav => fav.url === radio.url);
    },
    filterRadios() {
      // Funzione di filtro già implementata come computed property
    }
  },
  created() {
    this.getRadios();
  }
};
</script>

<style>
.overflow-ellipsis {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}
</style>


















