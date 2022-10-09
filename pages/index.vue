<template>
  <div class="root d-flex h-100 text-center text-white">

    <div class="cover-container d-flex w-100 h-100 p-3 mx-auto flex-column">

      <Header />

      <main class="main px-3">
        <h1>The Krack Fox kidnapped the Dirtbirds and rugged the project!</h1>
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
            <b-button @click="approveDirtbirdsRevived" :disabled="txInProgress"
              class="btn btn-lg btn-secondary fw-bold border-white bg-white">
              Approve Dirtbirds Revived
            </b-button>
          </p>
        </div>
        <div v-else-if="!txInProgress">
          <b-row>
            <b-col>
              <p class="lead">
                <a @click="wrap" 
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white">
                  Wrap Dirtbirds 
                </a>
              </p>
            </b-col>
            <b-col>
              <p class="lead">
                <a @click="unwrap" 
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white" >
                  Unwrap Dirtbirds Revived 
                </a>
              </p>
            </b-col>
          </b-row>
           <b-row>
            <b-col>
              <p class="lead">
                <a @click="mint(5)" 
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white info">
                  Mint 5 Dirtbirds 
                </a>
              </p>
            </b-col>
            <b-col>
              <p class="lead">
                <a @click="mint(100)" 
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white info" >
                  Mint 100 Dirtbirds 
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
        <!-- if txInProgress -->
        <div v-else>
          <b-row>
            <b-col>
              <p class="lead">
                <b-button @click="wrap" disabled
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white">
                  Wrap Dirtbirds 
                </b-button>
              </p>
            </b-col>
            <b-col>
              <p class="lead">
                <b-button @click="unwrap" disabled
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white" >
                  Unwrap Dirtbirds Revived 
                </b-button>
              </p>
            </b-col>
          </b-row>
           <b-row>
            <b-col>
              <p class="lead">
                <b-button @click="mint(5)" disabled
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white info">
                  Mint 5 Dirtbirds 
                </b-button>
              </p>
            </b-col>
            <b-col>
              <p class="lead">
                <b-button @click="mint(20)" disabled
                  class="btn btn-lg btn-secondary fw-bold border-white bg-white info" >
                  Mint 20 Dirtbirds 
                </b-button>
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

        <div class="details">
          <h4>What is this?</h4>
          <p>Dirtbirds Revived is a wrapper contract for Dirtbirds, when you wrap your Dirtbird NFT the future royalties go to Dirtbirds Revived's contract rather than the original dev. You can wrap and unwrap at any time.</p>
        </div>
      </main>

      <Footer />

      <!--<Tutorial/>-->
    </div>
  </div>
</template>

<script>
import { ethers } from "ethers";
import dirtbirdsContractJSON from "../static/TempBirds.json"; 
import freebirdsContractJSON from "../static/FreeBirds.json"; 
import axios from 'axios';
//import Moralis  from 'moralis';
//import { EvmChain } from '@moralisweb3/evm-utils';

export default {
  name: 'IndexPage',
  data() {
      return {
        dirtbirds_address: "0x79351db2014C28B6e44c77a7ca64f7401AC00D76",
        freebirds_address: "0xd4E8ddC9271CAE85A14Ae288879161b97Cf58E95",
        provider: false,
        signer: false,
        walletAddress: false,
        dirtbirdsContract: false,
        freebirdsContract: false,
        isApproved: false,
        dbTokens: false,
        walletTokens: false,
        walletTokenIds: [],
        numTokens: 0,
        fbTokens: false,
        fbWalletTokens: false,
        fbWalletTokenIds: [],
        numFBTokens: 0,
        txInProgress: false
      }
  },
  computed: {
  },
  watch: {
  },
  mounted() {
    
  },
  methods: {
    async connectWallet() {
      console.log('connectWallet function'); 
      this.txInProgress = true;

      this.provider = new ethers.providers.Web3Provider(window.ethereum, "goerli");
      console.dir(this.provider);
      //NEEDS TO BE ON GOERLI - CHECK AND PROMPT

      this.signer = await this.provider.getSigner();
      console.dir(this.signer);
      this.walletAddress = await this.signer.getAddress();
      console.dir(this.walletAddress);

      this.dirtbirdsContract = new ethers.Contract(this.dirtbirds_address, dirtbirdsContractJSON.abi, this.signer);
      console.dir(this.dirtbirdsContract);
      this.isApproved = await this.dirtbirdsContract.isApprovedForAll(this.signer.getAddress(), this.freebirds_address)
  
      this.freebirdsContract = new ethers.Contract(this.freebirds_address, freebirdsContractJSON.abi, this.signer);
      console.dir(this.freebirdsContract);

      this.txInProgress = false;

      this.enumerateWalletBirds();

    },
    async approveDirtbirdsRevived(){
      this.txInProgress = true;
      const approve = await this.dirtbirdsContract.setApprovalForAll(this.freebirds_address, true);
      console.log("approve message is: "); 
      console.dir(approve);
      await approve.wait()
      console.log('approve transaction completed:');
      console.dir(approve);
      this.isApproved = approve;
      this.txInProgress = false;
    },
    async wrap(){
      this.txInProgress = true;
      var tokens = this.walletTokenIds;
      if(this.walletTokenIds.length > 29){ //fails if more than 29 at a time
        tokens = this.walletTokenIds.slice(29);
      }
      console.log('Tokens are:');
      console.dir(tokens);

      try {
        const wrap = await this.freebirdsContract.wrap(
             tokens, 
             {
               //gasPrice: gasPrice,
               gasLimit: 2000000
           });
        console.log("wrap message is: "); 
        console.dir(wrap);
        await wrap.wait()
        console.log('wrap transaction completed:');
        console.dir(wrap);
        this.fbWalletTokenIds = this.fbWalletTokenIds.push(this.walletTokenIds.slice(29));
        this.walletTokenIds = this.walletTokenIds.slice(30);
        this.txInProgress = false;
      } catch(err){
        console.dir(err);
        this.txInProgress = false;
      }
    },
     async unwrap(){
      this.txInProgress = true;
      var tokens = this.fbWalletTokenIds;
      if(this.fbWalletTokenIds.length > 29){ //fails if more than 29 at a time
        tokens = this.fbWalletTokenIds.slice(0, 29);
      }
      console.log('tokens are:');
      console.dir(tokens);

      try {
        const unwrap = await this.freebirdsContract.unwrap(
             tokens, 
             {
               //gasPrice: gasPrice,
               gasLimit: 2000000
           });
        console.log("unwrap message is: "); 
        console.dir(unwrap);
        await unwrap.wait()
        console.log('unwrap transaction completed:');
        console.dir(unwrap);
        this.walletTokenIds = this.walletTokenIds.push(this.fbWalletTokenIds.slice(29));
        this.fbWalletTokenIds = this.fbWalletTokenIds.slice(30);
        this.txInProgress = false;
      } catch(err){
        console.dir(err);
        this.txInProgress = false;
      }
    },
    async mint(x){
      console.log("User wants to mint " + x + " Dirtbirds on Goerli testnet.");
      //0.0001 ether
      //var value = x * ; //0.0001;
      var v = ethers.BigNumber.from(100000000000000);
      var value = v.mul(x);
      //const value = ethers.utils.formatEther(v);
      console.log('It will cost ' + value + ' Wei');
      this.txInProgress = true;
      const mint = await this.dirtbirdsContract.mint(x, {
              //gasPrice: estimate,
              value: value,
              gasLimit: 2000000, //so we dont get out_of_gas delays again
            });
      console.log("mint message is: "); 
      console.dir(mint);
      await mint.wait()
      console.log('mint transaction completed:');
      console.dir(mint);
    
      this.txInProgress = false;

    },
    async enumerateWalletBirds(){
      //get a list of all Dirtbird tokens and Freebird tokens for this wallet
      var that = this;

      /*const dbBalance = await this.dirtbirdsContract.balanceOf(this.walletAddress);
      console.log('dbBalance:');
      console.dir(dbBalance);
      const dbBalanceInt = ethers.utils.formatUnits(dbBalance, 0);
      const foundTokens = 0;
      const idArray = [];
      console.dir(dbBalanceInt);*/


      //DIRTBIRDS
      const options = {
        method: 'GET',
        url: 'https://deep-index.moralis.io/api/v2/nft/' + this.dirtbirds_address + '/owners',
        params: {chain: 'goerli', format: 'decimal'},
        headers: {accept: 'application/json', 'X-API-Key': 'ispEzI1Q35Sj3OGDrxcYblrva8t8fQyvnPlQNIDvL2tc1nvbrrRArM5WzPdrXuYe'}
      };

      axios
        .request(options)
        .then(function (response) {
          console.log(response.data.result);
          that.dbTokens = response.data.result;
          that.walletTokens = that.dbTokens.filter(i => i.owner_of == that.walletAddress.toLowerCase());
          console.log("Wallet's tokens are: ");
          console.dir(that.walletTokens);
          console.log(that.walletTokens.length);

          for(var i = 0; i < that.walletTokens.length; i++){ //29
            var num = Number(that.walletTokens[i].token_id);
            that.walletTokenIds[i] = num;
          }
          that.numTokens = that.walletTokenIds.length;
          console.log('walletTokenIds:');
          console.dir(that.walletTokenIds);
          //var test = [12,13,14,15,16]
          //console.dir(test);
          
        })
        .catch(function (error) {
          console.error(error);
        });



        //FREEBIRDS
        const options2 = {
          method: 'GET',
          url: 'https://deep-index.moralis.io/api/v2/nft/' + this.freebirds_address + '/owners',
          params: {chain: 'goerli', format: 'decimal'},
          headers: {accept: 'application/json', 'X-API-Key': 'ispEzI1Q35Sj3OGDrxcYblrva8t8fQyvnPlQNIDvL2tc1nvbrrRArM5WzPdrXuYe'}
        };

        axios
          .request(options2)
          .then(function (response) {
            console.log(response.data.result);
            that.fbTokens = response.data.result;
            that.fbWalletTokens = that.fbTokens.filter(i => i.owner_of == that.walletAddress.toLowerCase());
            console.log("Wallet's tokens are: ");
            console.dir(that.fbWalletTokens);
            console.log(that.fbWalletTokens.length);

            for(var i = 0; i < that.fbWalletTokens.length; i++){ //29
              var num = Number(that.fbWalletTokens[i].token_id);
              that.fbWalletTokenIds[i] = num;
            }
            that.numFBTokens = that.fbWalletTokenIds.length;
            console.log('fbWalletTokenIds:');
            console.dir(that.fbWalletTokenIds);
            //var test = [12,13,14,15,16]
            //console.dir(test);
            
          })
          .catch(function (error) {
            console.error(error);
          });

    }
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
