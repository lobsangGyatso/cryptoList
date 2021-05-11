<template>
  <v-app>
    <v-app-bar
      app
      color="primary"
      dark
    >
      <div class="d-flex align-center">
        <v-img
          alt="Vuetify Logo"
          class="shrink mr-2"
          contain
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-dark.png"
          transition="scale-transition"
          width="40"
        />

        <v-img
          alt="Vuetify Name"
          class="shrink mt-1 hidden-sm-and-down"
          contain
          min-width="100"
          src="https://cdn.vuetifyjs.com/images/logos/vuetify-name-dark.png"
          width="100"
        />
      </div>

      <v-spacer></v-spacer>

      <v-btn
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
        text
      >
        <span class="mr-2">Latest Release</span>
        <v-icon>mdi-open-in-new</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main>
      <v-data-table
        :headers="headers"
        :items="listOfCoin"
        :loading="loading"
        hide-default-footer
        class="elevation-1"
      >
        <template v-slot:item.name="{ item }">
          <span v-if="item.name !== ''" class="title text-center">{{item.name}}</span>
          <span v-else class="title orange--text text-center">will be soon updated</span>
        </template>
        <template v-slot:item.amount="{ item }">
          <span v-if="item.amount !== ''" class="text-center ">{{item.amount}}</span>
          <span v-else class="orange--text text-center title">will be soon updated</span>
        </template>
        <template v-slot:item.asset_type="{ item }">
          <span v-if="item.asset_type !== ''" class="text-center">{{item.asset_type}}</span>
          <span v-else class="title orange--text text-center">will be soon updated</span>
        </template>
        <template v-slot:item.logo="{ item }">
          <v-img :src="item.logo" height="100" width="100" v-if="item.logo !== ''"></v-img>
          <span v-else class="orange--text title">No Image</span>
        </template>
        <template v-slot:item.usrate="{item}">
          <v-btn v-if="item.ticker !== ''" @click="convertToUs(item.ticker)" tile>US Rate</v-btn>
          <v-btn v-else disabled="true">US Rate</v-btn>
        </template>
      </v-data-table>
      <v-dialog
        v-model="dialog"
        max-width="290"
      >
        <v-card>
          <v-card-title class="headline">
            In US Rate
          </v-card-title>

          <v-card-text>
            <span>$$ {{dollar}}</span>
          </v-card-text>
        </v-card>
      </v-dialog>
    </v-main>
  </v-app>
</template>

<script>
import Zabo from 'zabo-sdk-js'
export default {
  name: 'App',

  data: () => ({
    file: [
      { documentType: '', filename: '' }
    ],
    dialog: false,
    listOfCoin: [],
    listOfRate: [],
    loading: true,
    dollar: '',
    headers: [
      {
        text: 'TickerName',
        align: 'start',
        value: 'amount'
      },
      { text: 'Name', value: 'name' },
      { text: 'Type', value: 'asset_type' },
      { text: 'Logo', value: 'logo' },
      { text: 'USRate', value: 'usrate' }
    ]
    //
  }),
  created () {
    this.getCrypto()
  },
  methods: {
    addAnother () {
      this.file.push({
        documentType: '',
        filename: ''
      })
    },
    remove (index) {
      this.file.splice(index, 1)
    },
    submit () {
      console.log(this.file)
    },
    getCrypto () {
      Zabo.init({
        clientId: 'DtMjbFjuPcDxrvbSaLBbVQnkkZpkkzTvS4nTBbNrCNfWTmtsIeEPNeYlfnR5awdv',
        // apiKey: "d787dd684f55dc1106ffdbf30cc424de77843f3c",
        // secretKey: "6b1c01ff386211e7bde5569aad5e640a5ae01f7d7484527a1e81188f1d348d43",
        env: 'sandbox'
      }).then((zabo) => {
        this.l = zabo
        zabo.connect().onConnection((account) => {
          console.log(account)
          // exchange rate
          this.listOfCoin = account.balances
          this.loading = false
          zabo.currencies.getExchangeRates().then(response => {
            this.currr = response.data
          })
        }).onError(error => {
          console.error('account connection error:', error)
        })
      })
    },
    convertToUs (coinName) {
      this.currr.forEach(coin => {
        if (coin.from === coinName) {
          this.dollar = coin.rate
          this.dialog = true
        }
      })
    },
    getExchangeRates () {
      Zabo.currencies.exchangeRates().then(res => {
        console.log('sfasdfsdf', res)
      })
    },

    openPanel () {
      if (this.panel !== null) {
        this.panel = null
      } else {
        this.panel = [1]
      }
    },
    selectAll () {
      this.showViewMore = !this.showViewMore
    }
  }
}
</script>
