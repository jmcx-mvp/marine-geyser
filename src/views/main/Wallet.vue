<template>
  <div class="wallet-container">
    <div class="row">
      <div class="col text-center">
        <img class="home-logo" :src="'/static/logo/logo.png'" alt=""/>
      </div>
    </div>
    <div class="row">
      <div class="col text-center welcome-tit">{{ $t("common.welcome") }}</div>
    </div>
    <div class="row">
      <div class="col text-center welcome-txt">{{ $t("common.welcomeTxt") }}</div>
    </div>
    <div class="row">
      <div class="col-xl-6 col-lg-6 col-md-6 col-sm-12 col-xs-12">
        <div class="balance-col-div">
          <div class="row">
            <div class="col-3 d-flex justify-content-around">
              <img class="geyser-logo" :src="'/static/logo/logo.png'" alt=""/>
            </div>
            <div class="col-9">
              <p class="preview-tit">{{ $t("home.balance") }}</p>
              <p v-if="userBalance === false" class="preview-txt">{{ $t("home.status") }}</p>
              <p v-if="userBalance !== false" class="preview-txt">{{ this.$HelperTools.toFinancialVal(userBalance) }}</p>
            </div>
          </div>
          <div class="row stats-div">
            <div class="col stats-txt">{{ $t("home.pending") }}</div>
<!--            <div class="col text-right stats-txt">{{ tokenBalance }} {{ $t("token.name") }}</div>-->
          </div>
        </div>
      </div>
      <div class="col-xl-6 col-lg-6 col-md-6 col-sm-12 col-xs-12">
        <div class="supply-col-div">
          <div class="row">
            <div class="col">
              <p class="preview-tit">{{ $t("home.total") }}</p>
              <p v-if="!totalReward" class="preview-txt">{{ $t("home.status") }}</p>
              <p v-if="totalReward" class="preview-txt">{{ this.$HelperTools.toFinancialVal(totalReward) }}</p>
            </div>
          </div>
          <div class="row stats-div">
            <div class="col-7 stats-txt">{{ $t("home.rewards") }}</div>
            <div class="col-5 text-right stats-txt">{{ rewardsBalance }} {{ $t("token.name") }}</div>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col">
        <div class="wallet-connect-div">
          <div class="row">
            <div v-show="walletConnectView" class="col text-center wallet-connect-tit">
              {{ $t("wallet.connect") }}
            </div>
          </div>

          <div class="row">
            <div class="col d-flex justify-content-around">
              <button v-show="walletConnectView" type="submit" class="wallet-btn" @click="openMetamask">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" :alt="$t('wallet.metamask')"/>
                <h2 class="wallet-btn-name text-left">{{ $t("wallet.name") }}</h2>
              </button>
              <button v-show="!walletConnectView" type="submit" class="wallet-btn" @click="seeTheMenu">
                <img class="wallet-btn-icon text-right" :src="'/static/logo/logo.png'" alt=""/>
                <h2 class="wallet-btn-name text-left">{{ $t("wallet.seeTheMenu") }}</h2>
              </button>
            </div>
          </div>

          <div class="row" v-show="walletConnectError">
            <div class="col text-center wallet-error-txt">
              {{ $t("wallet.error") }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import chainOpt from "../../utils/web3/chainOperation";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import Decimals from "decimal.js"

window.mingingPoolInfo = {}
let opt = chainOpt.opt

const startBlock = 21060600
const rewardPerBlock = 1000

export default {
  name: "Wallet",
  components: { Loading },

  data() {
    return {
      ConnectWalletModalText: 'Waiting for connection',
      loading: false,
      walletConnectView: true,
      walletConnectError: false,

      tokenBalance: "0",
      rewardsBalance: "1,000",

      totalReward: false,
      userBalance: false,
    };
  },

  mounted() {
    if(chainOpt.wallet.isConnected()) {
      this.setWalletConnected()
    }
  },

  methods: {
    setWalletConnected() {
      opt.getBlockNum()
          .then(r=>{
            if(r > 0 && r > startBlock) {
              let totalReward = new Decimals(r).sub(startBlock)
              this.totalReward = totalReward.mul(rewardPerBlock).toDP(2, Decimals.ROUND_FLOOR).toString()
            } else {
              this.totalReward = "0"
            }
          })

      opt.userTokenBalance()
          .then(r=>{
            this.userBalance = r
          })
      this.loading = false
      this.walletConnectView = false
    },

    async openMetamask() {
      //wallet connect
      this.loading = true

      if(!chainOpt.wallet.isConnected()) {
        chainOpt.wallet.connect()
          .then(r=>{
            if(r === null) {
              //not connect, do nothing
              this.loading = false
              return
            }

            this.setWalletConnected()
          })
          .catch(()=>{
            //unknown error, do nothing
            this.loading = false
            this.walletConnectError = true
          })
      } else {
        //already connected
        this.setWalletConnected()
      }
    },
    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    }
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.wallet-container {
  max-width: 800px;
  margin: auto;
  height: 100%;
  padding-top: 60px;
  padding-bottom: 120px;

  .home-logo{
    width: 120px;
    height: 120px;
    margin-bottom: 30px;
  }

  .welcome-tit {
    font-size: 27px;
    color: $fontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
  }
  .welcome-txt {
    margin-bottom: 30px !important;
    color: $txtColor;
    font-family: $fontTxt;
  }

  .balance-col-div,
  .supply-col-div {
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 10px;
    margin-bottom: 15px !important;
    padding: 15px 0;
    background-color: $divBg;
  }

  .geyser-logo {
    width: 60px;
    height: 60px;
    margin: auto;
  }

  .preview-tit {
    font-size: 14px;
    font-family: $fontTxt;
    color: $txtColor;
  }
  .preview-txt {
    font-size: 27px;
    font-family: $fontTit;
    color: $titColor;
  }

  .stats-div {
    border-top-width: 1px;
    border-style: solid none none none;
    border-color: $divBorderColor;
    padding-top: 15px;
  }
  .stats-txt {
    font-size: 14px;
    font-family: $fontTxt;
    color: $txtColor;
  }

  .wallet-connect-div {
    //border-width: 1px;
    //border-style: solid;
    //border-color: $divBorderColor;
    //border-image: initial;
    //border-radius: 0;
    padding: 30px 15px;
    margin-bottom: 24px !important;
  }

  .wallet-connect-tit {
    font-size: 16px;
    color: $txtColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-bottom: 20px;
  }
  .wallet-error-txt {
    font-size: 16px;
    color: $errColor;
    line-height: 28px;
    font-family: $fontTxt;
    margin-top: 15px;
  }

  .wallet-btn {
    height: 80px;
    max-width: 480px;
    display: flex;
    justify-content: left;
    -webkit-box-align: center;
    align-items: center;
    color: $walletBtnColor;
    background-color: $btnBg;
    box-shadow: $btnBoxShadow-sushi 2px 2px 4px, $btnBoxShadow-sushi-2 -2px -2px 4px -1px;
    opacity: 1;
    cursor: pointer;
    border-radius: 15px;
    padding: 16px 10%;
    border-width: 2px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    transition: all 0.1s ease 0s;
    outline: $walletBtnOutline;
  }
  .wallet-btn:hover,
  .wallet-btn:focus,
  .wallet-btn:active {
    border-width: 2px;
    border-style: solid;
    border-color: $walletBtnBg;
    border-image: initial;
    border-radius: 15px;
    outline: $walletBtnOutline;
    color: $walletBtnBg;
    background-color: $walletBtnColor;
  }
  .wallet-btn-name {
    margin: 0 0 0 16px;
    letter-spacing: 2px;
    font-family: $fontTit;
    text-transform: capitalize;
    font-size: 18px;
  }
  .wallet-btn-icon {
    height: 40px;
    width: 40px;
  }
}
</style>
