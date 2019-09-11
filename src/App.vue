<template>
  <div id="app">
    <p>Address: {{ address }}</p>
    <p>Signature: {{ signature }}</p>
    <button :disabled="!address" @click="signinMessage">Signin message</button>
  </div>
</template>

<script>

export default {
  data: () => ({
    address: '',
    signature: '',
    error: null,
  }),
  created() {
    this.signin();
    this.onUpdateAddress();
  },
  methods: {
    async signin() {
      const { currentProvider } = window.web3;

      if (typeof currentProvider.enable === 'function') {
        await currentProvider.enable()
        this.getAddress();
      }
    },
    getAddress() {
      const { eth } = window.web3;

      eth.getAccounts((err, accounts) => {
        if (err) return;
        this.address = accounts[0];
      });
    },
    signinMessage() {
      const { personal } = window.web3;

      if (!this.address) {
        return;
      }

      personal.sign('Hello!', this.address, 'password', (err, signature) => {
        if (err) return;
        this.signature = signature;
      })
    },
    onUpdateAddress() {
      const { ethereum } = window;
      ethereum.on('accountsChanged', (addresses) => {
        this.address = addresses[0];
        this.signature = '';
      })
    }
  }
}
</script>
