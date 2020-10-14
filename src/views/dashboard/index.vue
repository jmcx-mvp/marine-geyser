<template>
  <div class="dashboard-container">
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
      <div class="col-xl-4 col-lg-4 col-md-4 col-sm-12 col-xs-12 text-center"
           v-for="(product, index) in products" :key="index">
        <product :product-data="product"
                 :product-index="LPTokenToPoolID(product.LPToken)"
                 :pool-enabled="product.stat"></product>
      </div>
    </div>

    <loading :active.sync="loading"
             :can-cancel="false"
             :is-full-page="true"></loading>
  </div>
</template>

<script>
import Product from "./Product";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import {LPTokenToPoolID} from "@/utils/web3/constant";

export default {
  name: "dashboard",
  components: {Product, Loading},

  data() {
    return {
      allBalance: null,
      poolInfo: null,
      loading: false,

      products: [
        {
          img: "/static/icon/MAR-ETH.png",
          name: "MAR-ETH",
          txt: "Deposit MAR-ETH LP Earn MAR",
          tx: "MAR-ETH",
          apy: "1666",
          stat: false,
          dateTime: "",
          double: true,
          LPToken: "",
        },
        {
          img: "/static/icon/MAR-CREDIT.png",
          name: "MAR-CREDIT",
          txt: "Deposit MAR-CREDIT LP Earn MAR",
          tx: "MAR-CREDIT",
          apy: "1666",
          stat: false,
          dateTime: "",
          double: true,
          LPToken: "",
        },
        {
          img: "/static/icon/USDT-ETH.png",
          name: "USDT-ETH",
          txt: "Deposit USDT-ETH LP Earn MAR",
          tx: "USDT-ETH",
          apy: "888",
          stat: true,
          dateTime: "",
          double: false,
          LPToken: "0xd1106dB81792a47DBb702Ad714ca63eF83463309",
        },
        {
          img: "/static/icon/USDC-ETH.png",
          name: "USDC-ETH",
          txt: "Deposit USDC-ETH LP Earn MAR",
          tx: "USDC-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "0xb4e16d0168e52d35cacd2c6185b44281ec28c9dc",
        },
        {
          img: "/static/icon/DAI-ETH.png",
          name: "DAI-ETH",
          txt: "Deposit DAI-ETH LP Earn MAR",
          tx: "DAI-ETH",
          apy: "888",
          stat: true,
          dateTime: "",
          double: false,
          LPToken: "0xB10cf58E08b94480fCb81d341A63295eBb2062C2",
        },
        {
          img: "/static/icon/sUSD-ETH.png",
          name: "sUSD-ETH",
          txt: "Deposit sUSD-ETH LP Earn MAR",
          tx: "sUSD-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "0xf80758ab42c3b07da84053fd88804bcb6baa4b5c",
        },
        {
          img: "/static/icon/RenBTC-ETH.png",
          name: "RenBTC-ETH",
          txt: "Deposit RenBTC-ETH LP Earn MAR",
          tx: "RenBTC-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "0x81fbef4704776cc5bba0a5df3a90056d2c6900b3",
        },
        {
          img: "/static/icon/WBTC-ETH.png",
          name: "WBTC-ETH",
          txt: "Deposit WBTC-ETH LP Earn MAR",
          tx: "WBTC-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "0xbb2b8038a1640196fbe3e38816f3e67cba72d940",
        },
        {
          img: "/static/icon/CREDIT-ETH.png",
          name: "CREDIT-ETH",
          txt: "Deposit CREDIT-ETH LP Earn MAR",
          tx: "CREDIT-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/CREAM-ETH.png",
          name: "CREAM-ETH",
          txt: "Deposit CREAM-ETH LP Earn MAR",
          tx: "CREAM-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/LEND-ETH.png",
          name: "LEND-ETH",
          txt: "Deposit LEND-ETH LP Earn MAR",
          tx: "LEND-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/SNX-ETH.png",
          name: "SNX-ETH",
          txt: "Deposit SNX-ETH LP Earn MAR",
          tx: "SNX-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/SUSHI-ETH.png",
          name: "SUSHI-ETH",
          txt: "Deposit SUSHI-ETH LP Earn MAR",
          tx: "SUSHI-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/YFII-ETH.png",
          name: "YFII-ETH",
          txt: "Deposit YFII-ETH LP Earn MAR",
          tx: "YFII-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/YAMv2-ETH.png",
          name: "YAMv2-ETH",
          txt: "Deposit YAMv2-ETH LP Earn MAR",
          tx: "YAMv2-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
        {
          img: "/static/icon/$AAPL-ETH.png",
          name: "$AAPL-ETH",
          txt: "Deposit $AAPL-ETH LP Earn MAR",
          tx: "$AAPL-ETH",
          apy: "888",
          stat: false,
          dateTime: "",
          double: false,
          LPToken: "",
        },
      ]
    }
  },

  methods: {
    LPTokenToPoolID: LPTokenToPoolID,
    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.dashboard-container {
  width: 100%;
  height: 100%;

  .home-logo{
    width: 120px;
    height: 120px;
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
}
</style>
