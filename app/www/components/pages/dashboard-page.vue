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

            <v-card v-if="qr">
              <v-img src="images/background2-min.jpg" aspect-ratio="2.75"></v-img>

              <v-card-title primary-title>
                <div>
                  <h3 class="headline mb-0 charging">Charging</h3><br>
                  <div>
                    <div style="float: left; margin-bottom: 4px">
                      <v-progress-circular :rotate="-90" :size="100" :width="15" :value="qr.soc_bms || qr.soc_display || 0"
                        color="#fc9928">
                        {{ qr.soc_display || qr.soc_bms || 0 }} %
                      </v-progress-circular>
                    </div>
                    <div style="margin-left: 4px;float: right;text-align: left;">
                      <div v-if="qr.estimatedTime">
                        <h4>Estimated time left (full charge): </h4>
                        {{
                        qr.slow_charge_port ? convertDecimalTime(qr.estimatedTime.estimatedSlowTime) :
                        qr.normal_charge_port ? convertDecimalTime(qr.estimatedTime.estimatedNormalTime) :
                        qr.rapid_charge_port ? convertDecimalTime(qr.estimatedTime.estimatedFastTime) : '00:00'
                        }}h
                      </div>
                      <!-- TODO: Not available yet -->
                      <div v-if="false">
                        <h4>Estimated time left (75% user notification): </h4> 00:17h
                      </div>
                    </div>
                  </div>
                </div>
              </v-card-title>

              <v-card-actions>
                <v-btn flat color="orange" @click="dialog = true">Notify me on charge finished</v-btn>
              </v-card-actions>
              <v-card-actions>
                <v-btn flat color="orange" @click="dialog = true">Notify driver that you need to charge</v-btn>
              </v-card-actions>
            </v-card>

            <v-layout row justify-center>
              <v-dialog v-model="dialog" persistent max-width="290">
                <v-card>
                  <v-card-title class="headline">Unavailable</v-card-title>
                  <v-card-text>This feature is currently under development and will be activated as soon as possible.
                    Please be patient.</v-card-text>
                  <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn color="green darken-1" flat @click="dialog = false">Ok, cool</v-btn>
                  </v-card-actions>
                </v-card>
              </v-dialog>
            </v-layout>

            <v-alert :value="loading" type="info">
              Retrieving information from QR code. Please wait.
            </v-alert>

            <v-alert :value="!loading && denied" type="warning">
              The driver is currently not charging or the data is too old.
            </v-alert>

            <v-alert :value="aborted && !denied" type="error">
              Information could not be resolved.
            </v-alert>

            <v-alert :value="notFound" type="error">
              QR Code not found.
            </v-alert>

          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
    <v-footer app fixed>
      <span style="margin: auto">&copy; 2019 EVNotify - <a href="https://evnotify.de/imprint.html">Legal Notice</a></span>
    </v-footer>
  </v-app>
</template>

<script>
  export default {
    data: () => ({
      drawer: null,
      qr: false,
      dialog: false,
      loading: true,
      denied: false,
      aborted: false,
      notFound: false
    }),
    mounted() {
      this.$http.get(RESTURL + 'qr', {
        params: {
          code: location.search.split('code=')[1]
        }
      }).then(response => {
        console.log(response);
        this.loading = this.aborted = this.notFound = this.denied = false;
        this.qr = response.body;
      }, err => {
        console.log(err);
        this.loading = this.qr = false;
        this.aborted = err.status !== 404;
        this.notFound = err.status === 404;
        this.denied = err.status === 401;
      });
    },
    methods: {
      /**
       * Converts the decimal time into hh:mm format (e.g.: 0.5h will be converted to 00:30h)
       * @param {Number} time the time to convert
       * @returns {String} the formatted time string
       */
      convertDecimalTime: function (time) {
        if (isNaN(parseInt(time))) return '00:00';

        var sign = ((time < 0) ? '-' : ''),
          min = Math.floor(Math.abs(time)),
          sec = Math.floor((Math.abs(time) * 60) % 60);

        return sign + (((min < 10) ? '0' : '')) + min + ':' + (((sec < 10) ? '0' : '')) + sec;
      }
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