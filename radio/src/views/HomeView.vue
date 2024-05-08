<template>
  <v-container>
    <v-row>
      <v-col v-for="(radio, index) in radios" :key="index" cols="3">
        <v-card color="white">
          <div class="d-flex flex-column justify-space-between" style="height: 100%;">
            <div class="d-flex flex-column justify-space-between">
              <v-card-title class="text-h5 overflow-ellipsis">{{ radio.name }}</v-card-title>
              <v-avatar class="mx-auto mb-2" rounded="0" size="100">
                <v-img :src="getRadioLogo(radio)" contain></v-img>
              </v-avatar>
            </div>
            <v-card-actions class="justify-center">
              <v-btn icon class="mdi mdi-play"></v-btn>
            </v-card-actions>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'HomeView',
  data() {
    return {
      radios: [],
      defaultLogo: 'https://fiverr-res.cloudinary.com/images/t_main1,q_auto,f_auto,q_auto,f_auto/gigs/295505491/original/cfbfdfff1c5e32c8ccd5e70f86c8e434a374f431/design-attractive-radio-logo.jpg'
    };
  },
  methods: {
    getRadios() {
      fetch('https://nl1.api.radio-browser.info/json/stations/search?limit=100&countrycode=IT&hidebroken=true&order=clickcount&reverse=true')
        .then(response => response.json())
        .then(data => {
          this.radios = data;
          console.log(data);
        });
    },
    getRadioLogo(radio) {
      return radio.favicon || this.defaultLogo;
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




