<template>
  <div class="root d-flex h-100 text-center text-white">

    <div class="cover-container d-flex w-100 h-100 p-3 mx-auto flex-column">

      <Header />

      <main class="main px-3">
        <h1>About</h1>
        <p class="lead">Wrap your Dirtbirds here to save them from contributing future royalties to the rug devs.***</p>
        <div v-if="!provider">
          <p class="lead">
            <a @click="connectWallet" :disabled="txInProgress"
              class="btn btn-lg btn-secondary fw-bold border-white bg-white">
              Connect Wallet 
              <b-spinner v-if="txInProgress"></b-spinner>
            </a>
          </p>
        </div>
        <div v-else-if="!isApproved">  
          <p class="lead">
            <a @click="approveSafeArbor" :disabled="txInProgress"
              class="btn btn-lg btn-secondary fw-bold border-white bg-white">
              Approve Dirtbirds Revived
              <b-spinner v-if="txInProgress"></b-spinner>
            </a>
          </p>
        </div>
        <div v-else>
          <b-row>
            <b-col>
              <p class="lead">
                <a @click="wrap" :disabled="!numTokens || txInProgress"
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white">
                  Wrap Dirtbirds 
                  <b-spinner v-if="txInProgress"></b-spinner>
                </a>
              </p>
            </b-col>
            <b-col>
              <p class="lead">
                <a @click="unwrap" :disabled="!numFBTokens || txInProgress"
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white" >
                  Unwrap Dirtbirds Revived 
                  <b-spinner v-if="txInProgress"></b-spinner>
                </a>
              </p>
            </b-col>
          </b-row>
          <p class="lead">
            You have {{ numTokens }} Dirtbirds and {{ numFBTokens}} Dirtbirds Revived!
          </p>
          <p class="lead" v-if="(numTokens > 29) || (numFBTokens > 29) ">
            <i>***Maximum of 29 tokens per wrap or unwrap transaction.</i>
          </p>
        </div>
       
      </main>

      <Footer />

    </div>
  </div>
</template>

<script>


export default {
  name: 'AboutPage',
  data() {
      return {
      }
  },
  computed: {
  },
  watch: {
  },
  mounted() {
    console.dir(this.$route);
  },
  methods: {
    
  }
}
</script>
<style>

.bd-placeholder-img {
  font-size: 1.125rem;
  text-anchor: middle;
  -webkit-user-select: none;
  -moz-user-select: none;
  user-select: none;
}

@media (min-width: 768px) {
  .bd-placeholder-img-lg {
    font-size: 3.5rem;
  }
}

.btn-secondary,
.btn-secondary:hover,
.btn-secondary:focus {
  color: #333;
  text-shadow: none; 
}

body {
  text-shadow: 0 .05rem .1rem rgba(0, 0, 0, .5);
  box-shadow: inset 0 0 5rem rgba(0, 0, 0, .5);
}

.cover-container {
  max-width: 58em;
}

.nav-masthead .nav-link {
  padding: .25rem 0;
  font-weight: 700;
  color: rgba(255, 255, 255, .5);
  background-color: transparent;
  border-bottom: .25rem solid transparent;
}

.nav-masthead .nav-link:hover,
.nav-masthead .nav-link:focus {
  border-bottom-color: rgba(255, 255, 255, .25);
}

.nav-masthead .nav-link + .nav-link {
  margin-left: 1rem;
}

.nav-masthead .active {
  color: #fff;
  border-bottom-color: #fff;
}

.header {
  margin-bottom: 100px;
}

.header .title {
    float: left !important;
}

.header .menu {
    float: right !important;
}

.main {
  margin-bottom: 100px;
  text-align: center !important;
}

.footer {
  text-align: center !important;
}

.root {
  background-color: #212529;
}

h1, .lead {
  margin-bottom: 30px;
}

.details {
  margin-top: 100px;
}
</style>
