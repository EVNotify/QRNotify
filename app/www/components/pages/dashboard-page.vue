<template>
  <v-app id="inspire" dark>
    <v-navigation-drawer v-model="drawer" clipped fixed app>
      <v-list dense>
        <v-list-tile>
          <v-list-tile-action>
            <v-icon>dashboard</v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title>Dashboard</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar app fixed clipped-left>
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title>QRNotify</v-toolbar-title>
    </v-toolbar>
    <v-content>
      <v-container fluid fill-height>
        <v-layout justify-center align-center>
          <v-flex shrink>

            <v-card>
              <v-img src="images/background2-min.jpg" aspect-ratio="2.75"></v-img>

              <v-card-title primary-title>
                <div>
                  <h3 class="headline mb-0 charging">Charging</h3><br>
                  <div>
                    <div style="float: left; margin-bottom: 4px">
                      <v-progress-circular :rotate="-90" :size="100" :width="15" :value="10" color="#fc9928">
                        10 %
                      </v-progress-circular>
                    </div>
                    <div style="margin-left: 4px;float: right;text-align: left;">
                      <div>
                        <h4>Estimated time left (full charge): </h4> 00:28h
                      </div>
                      <div>
                        <h4>Estimated time left (75% user notification): </h4> 00:17h
                      </div>
                    </div>
                  </div>
                </div>
              </v-card-title>

              <v-card-actions>
                <v-btn flat color="orange">Notify me on charge finished</v-btn>
              </v-card-actions>
              <v-card-actions>
                <v-btn flat color="orange">Notify driver that you need to charge</v-btn>
              </v-card-actions>
            </v-card>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
    <v-footer app fixed>
      <span style="margin: auto">&copy; 2018 EVNotify - <a href="https://evnotify.de/imprint.html">Legal Notice</a></span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      drawer: null
    }),
    props: {
      source: String
    }
  }
</script>

<style scoped>
  .charging:after {
    content: ' .';
    animation: dots 1s steps(5, end) infinite;
  }

  @keyframes dots {

    0%,
    20% {
      color: rgba(0, 0, 0, 0);
      text-shadow:
        .25em 0 0 rgba(0, 0, 0, 0),
        .5em 0 0 rgba(0, 0, 0, 0);
    }

    40% {
      color: white;
      text-shadow:
        .25em 0 0 rgba(0, 0, 0, 0),
        .5em 0 0 rgba(0, 0, 0, 0);
    }

    60% {
      text-shadow:
        .25em 0 0 white,
        .5em 0 0 rgba(0, 0, 0, 0);
    }

    80%,
    100% {
      text-shadow:
        .25em 0 0 white,
        .5em 0 0 white;
    }
  }
</style>