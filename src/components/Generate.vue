<template>
  <div class="generate hero is-dark is-bold is-fullheight">
    <div class="hero-body">
      <div class="container">
        <div class="columns">
          <div class="column">
            <h1 class="title" v-if="idealWallet === ''">Arweave Wallet Miner</h1>
            <div class="card" v-if="idealWallet !== ''">
              <div class="card-content">
                <h6 class="subtitle">ADDRESS:</h6>
                <h1 class="title monospace">{{ idealWallet }}</h1>
              </div>
            </div>
          </div>
          <div class="column">
            <input class="input is-success" type="text" placeholder="Your ideal phrase" v-model="idealPhrase">
            <button v-if="idealWallet.substring(0, idealPhrase.length) !== idealPhrase" class="button is-fullwidth" @click="generateWallet">Generate</button>
            <a v-if="idealWallet.substring(0, idealPhrase.length) === idealPhrase" class="button is-success is-fullwidth" :href='downloadURL' download="keyfile.json">Download Keyfile</a>
          </div>
        </div>
      </div>
    </div>    
  </div>
</template>

<script>
import Arweave from "arweave";

const client = Arweave.init({
  host: 'arweave.net',
  port: 443,
  protocol: 'https',
  timeout: 20000,
  logging: false,
});

export default {
  name: 'Generate',
  data() {
    return {
      idealWallet: "",
      keyfile: {},
      idealPhrase: "",
      downloadURL: ""
    }
  },
  methods: {
    async generateWallet() {
      while (this.idealWallet.substring(0, this.idealPhrase.length) !== this.idealPhrase) {
        // Generate new wallet and check again
        this.keyfile = await client.wallets.generate();
        this.idealWallet = await client.wallets.jwkToAddress(this.keyfile);
      }
      this.generateURL();
    },
    generateURL() {
      console.log(this.keyfile);
      this.downloadURL = URL.createObjectURL(new Blob([JSON.stringify(this.keyfile)], {type: "application/json"}));
    }
  }
}
</script>

<style lang="css" scoped>
@font-face{
  font-family: 'JetBrainsMono';
  src: url('https://cdn.jsdelivr.net/gh/JetBrains/JetBrainsMono/web/woff2/JetBrainsMono-Regular.woff2') format('woff2'),
    url('https://cdn.jsdelivr.net/gh/JetBrains/JetBrainsMono/web/woff/JetBrainsMono-Regular.woff') format('woff'),
    url('https://cdn.jsdelivr.net/gh/JetBrains/JetBrainsMono/ttf/JetBrainsMono-Regular.ttf') format('truetype');
  font-weight: 400;
  font-style: normal;
}

.monospace {
  font-family: "JetBrainsMono";
}

.card-content h1, .card-content h6 {
  color: black;
}
</style>
