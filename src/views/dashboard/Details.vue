<template>
  <div class="detail-div">
    <div class="row">
      <div class="col text-center">
        <img class="home-logo" :src="'/static/icon/' + this.$route.params.tx_id + '.png'" alt=""/>
<!--        <img class="home-logo" :src="'/static/actions/track.png'" alt=""/>-->
      </div>
    </div>
    <div class="row">
      <div class="col text-center welcome-tit">{{ this.$route.params.tx_id }}</div>
    </div>
    <div class="row">
      <div class="col text-center welcome-txt">{{ $t("common.welcomeTxt") }}</div>
    </div>

    <div class="row">
      <div class="col-xl-2 col-lg-2 col-md-1 col-sm-12 col-xs-12"></div>
      <div class="col-xl-4 col-lg-4 col-md-5 col-sm-12 col-xs-12 text-center">
        <div class="product-div">
          <div class="row">
            <div class="col">
              <div class="product-logo-div">
                <img class="product-logo" :src="'/static/actions/withdraw.png'" alt=""/>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <p class="pdt-name">{{ marRewards }}</p>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <p class="pdt-txt">{{ $t("details.harvest") }}</p>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <input type="submit" class="btn btn-select" id="withdrawBtn"
                     :value="$t('action.harvest')"
                     data-toggle="modal" data-target="#harvestModal">
            </div>
          </div>
        </div>
      </div>

      <div class="col-xl-4 col-lg-4 col-md-5 col-sm-12 col-xs-12 text-center">
        <div class="product-div">
          <div class="row">
            <div class="col">
              <div class="product-logo-div">
                <img class="product-logo" :src="'/static/actions/deposit.png'" alt=""/>
              </div>
            </div>
          </div>
          <div class="row"><div class="col"><p class="pdt-name">{{ stakingAmount }}</p></div></div>
          <div class="row"><div class="col"><p class="pdt-txt">{{ $t("details.staked") }}</p></div></div>
          <div class="row">
            <div v-show="enabled" class="col-6 btn-lr-l">
              <input type="submit" class="btn btn-select" :value="$t('action.deposit')"
                                    data-toggle="modal" data-target="#stakeModal"
                                    @click="showStakingModal(false)">
            </div>

            <div v-show="enabled" class="col-6 btn-lr-r">
              <input type="submit" class="btn btn-select" :value="$t('action.withdraw')"
                     data-toggle="modal" data-target="#stakeModal"
                     @click="showStakingModal(true)">
            </div>

            <div v-show="!enabled" class="col">
              <input type="submit" class="btn btn-select" id="approveBtn"
                     :value="approveBtnDisable ? $t('action.approveWait') : $t('action.approve')"
                     @click="enable">
            </div>

          </div>
        </div>
      </div>

      <div class="col-xl-2 col-lg-2 col-md-1 col-sm-12 col-xs-12"></div>
    </div>

    <div class="row">
      <div class="col d-flex justify-content-around">
        <button type="submit" class="d-wallet-btn" @click="seeTheMenu">
          <img class="d-wallet-btn-icon text-right" :src="'/static/logo/logo.png'" alt=""/>
          <h2 class="d-wallet-btn-name text-left">{{ $t("wallet.seeTheMenu") }}</h2>
        </button>
      </div>
    </div>

   <!--               modal                   -->
    <div class="modal fade" id="stakeModal">
      <div class="modal-dialog">
        <div class="modal-content text-center">
          <div class="modal-body">
            <div class="row">
              <div class="col">
                <img class="modal-logo" :src="'/static/actions/burn.png'" alt=""/>
                <p class="modal-tit">{{ modalForWithdraw ? $t("modal.tit2") : $t("modal.tit1") }} {{ this.$route.params.tx_id }}</p>
                <input v-show="!modalForWithdraw" type="text" v-model="willStake" placeholder="0" class="staking-input">
                <input v-show="modalForWithdraw" type="text" v-model="willWithdraw" placeholder="0" class="staking-input unstake">
              </div>
            </div>
            <div class="row">
              <div class="col text-left balance-tit">
                {{ $t("common.balance")}}
                <b class="balance-txt" v-show="!modalForWithdraw">{{ cBalance }}</b>
                <b class="balance-txt" v-show="modalForWithdraw">{{ stakingAmount }}</b>
              </div>
              <div class="col text-right">
                <input type="submit" class="btn btn-balance" :value="$t('common.max')"
                       @click="maxBalance">
              </div>
            </div>
            <div class="row">
              <div class="col">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">{{ $t("common.cancel") }}</button>
              </div>
              <div class="col">
                <input type="submit" class="btn btn-secondary" :value="$t('common.confirm')"
                       v-show="!modalForWithdraw && (cBalance > 0)"
                       @click="staking">
                <input type="submit" class="btn btn-secondary" :value="$t('common.confirm')"
                       v-show="!modalForWithdraw && (cBalance == 0)"
                       disabled>
                <input type="submit" class="btn btn-secondary" :value="$t('common.confirm')"
                       v-show="modalForWithdraw && (stakingAmount > 0)"
                       @click="withdraw">
                <input type="submit" class="btn btn-secondary" :value="$t('common.confirm')"
                       v-show="modalForWithdraw && (stakingAmount == 0)"
                       disabled>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="modal fade" id="harvestModal">
      <div class="modal-dialog">
        <div class="modal-content text-center">
          <div class="modal-body">
            <p class="welcome-tit">{{ $t("modal.tit3")}} {{ this.$route.params.tx_id }} Pool MAR</p>
            <div class="row">
              <div class="col">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">{{ $t("common.cancel") }}</button>
              </div>
              <div class="col">
                <input type="submit" class="btn btn-secondary" :value="$t('common.confirm')" @click="harvest">
              </div>
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
import $ from "jquery";
import Loading from 'vue-loading-overlay';
import 'vue-loading-overlay/dist/vue-loading.css';
import Decimal from "decimal.js"

let opt = chainOpt.opt

export default {
  name: "Details",

  components: {
    Loading
  },

  data() {
    return {
      enabled: false,
      loading: false,
      willStake: 0,
      willWithdraw: 0,
      pid: null,
      poolInfo: null,
      modalForWithdraw: false,
      querying: false,
      cBalance: 0,
      approveBtnDisable: false
    }
  },

  watch: {
    enabled() {
      if (this.enabled) {
        $(this.$el).find(".staking-input").removeAttr("disabled")
      } else {
        $(this.$el).find(".staking-input").attr("disabled", "disabled")
      }
    },
  },

  mounted() {
    this.pid = this.$route.params.pool_idx
    if (!this.$route.params.pool_info) {
      opt.poolInfo(this.pid)
          .then(r => {
            this.poolInfo = r;
            this.cBalance = this.poolInfo.uniBalance
            this.loopQueryGlobalPoolInfo()
          })
    } else {
      this.loopQueryGlobalPoolInfo()
    }

    if (this.marRewards === "0") {
      $(this.$el).find("#withdrawBtn").attr("disabled", "disabled")
    } else {
      $(this.$el).find("#withdrawBtn").removeAttr("disabled")
    }

    setTimeout(async _ => {
      this.enabled = await opt.isPoolEnable(this.pid).catch(e => {
        return false
      })

      if (!this.enabled) {
        this.$nextTick(_ => {
          $(this.$el).find(".staking-input").attr("disabled", "disabled")
        })
      }
    }, 500)
  },

  computed: {
    stakingAmount() {
      return this.poolInfo ? this.poolInfo.user.stakeIn : 0
    },

    marRewards() {
      let ret = this.poolInfo ? this.poolInfo.userToCollect : "0"
      if (ret === "0") {
        $(this.$el).find("#withdrawBtn").attr("disabled", "disabled")
      } else {
        $(this.$el).find("#withdrawBtn").removeAttr("disabled")
      }

      return ret
    }
  },

  methods: {
    seeTheMenu() {
      this.$router.push({name: "Dashboard"})
    },

    async loopQueryGlobalPoolInfo() {
      await this.queryGlobalPoolInfo()
      setTimeout(_ => {
        this.loopQueryGlobalPoolInfo()
      }, 5000)
    },

    async queryGlobalPoolInfo() {
      if (this.querying) {
        return
      }
      this.querying = true

      if (!window.allPoolInfo[this.pid]) {
        this.poolInfo = await opt.poolInfo(this.pid)
        this.cBalance = this.poolInfo.uniBalance
        this.querying = false
        return
      }

      this.poolInfo = window.allPoolInfo[this.pid]
      this.cBalance = this.poolInfo.uniBalance
      this.querying = false
    },

    async loopCheckEnabled() {
      this.enabled = await opt.isPoolEnable(this.pid).catch(e => {
        return false
      })

      if (this.enabled) {
        return
      }

      setTimeout(this.loopCheckEnabled, 5000)
    },

    async enable() {
      this.loading = true

      let enabled = await opt.isPoolEnable(this.pid)
      if (enabled) {
        this.enabled = true
        this.loading = false
        return
      }

      enabled = await opt.enableDeposit(this.pid)

      this.loading = false
      if (enabled === null) {
        return
      }
      $(this.$el).find("#approveBtn").attr("disabled", "disabled")
      this.approveBtnDisable = true
      await this.loopCheckEnabled()
    },

    async staking() {
      this.loading = true

      try  {
        let willS = new Decimal(this.willStake)
        if(willS.gt(this.cBalance)) {
          this.willStake = this.cBalance
        }
      } catch (e) {
        this.willStake = this.cBalance
      }

      let stakeAmount = this.willStake
      try {
        stakeAmount = new Decimal(stakeAmount)
      } catch (e) {
        //todo: show alert to tell user input not a number
        return
      }

      let enabled = await opt.isPoolEnable(this.pid)
      if (!enabled) {
        this.enabled = false
        this.loading = false
        return
      }

      let uniAmount = await opt.userUniBalance(this.pid)
      if (stakeAmount.gt(uniAmount)) {
        //todo: show some alert to tell user they don't have enough uni token to stake
        return
      }

      let tx = await opt.deposit(this.pid, stakeAmount)

      if (tx !== null) {
        console.log("transaction send success, tx hash is: ", tx)
      } else {

      }

      this.loading = false
      $('#stakeModal').modal('hide')
    },

    async harvest() {
      this.loading = true
      let zeroWithdraw = new Decimal(0)
      await opt.withdraw(this.pid, zeroWithdraw)
      this.loading = false
      $('#harvestModal').modal('hide')
    },

    async withdraw() {
      this.loading = true

      try  {
        let willW = new Decimal(this.willWithdraw)
        if(willW.gt(this.stakingAmount)) {
          this.willWithdraw = this.stakingAmount
        }
      } catch (e) {
        this.willWithdraw = this.stakingAmount
      }

      let willWithdraw = 0
      try {
        willWithdraw = new Decimal(this.willWithdraw)
      } catch (e) {
        //todo: show alert to tell user input not a number
        return
      }

      if (willWithdraw.gt(this.poolInfo.orgStake)) {
        //todo: show some alert to tell user they don't have enough staked uni token to withdraw
        return
      }
      await opt.withdraw(this.pid, willWithdraw)
      $('#stakeModal').modal('hide')
      this.loading = false
    },

    showStakingModal(isWithdraw = false) {
      this.modalForWithdraw = isWithdraw
    },

    maxBalance() {
      if (this.modalForWithdraw) {
        this.willWithdraw = this.poolInfo ? this.poolInfo.user.stakeIn : 0;
      } else {
        this.willStake = this.cBalance;
      }
    },
  },
}
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
@import "../../styles/colors.scss";
@import "../../styles/fonts.scss";

.detail-div {

  .home-logo{
    width: 150px;
    height: 150px;
  }

  .welcome-tit {
    font-size: 27px;
    color: $fontColor;
    letter-spacing: 2px;
    font-family: $fontTit;
    margin: 15px 0 0;
  }
  .welcome-txt {
    margin-bottom: 30px !important;
    color: $txtColor;
    font-family: $fontTxt;
  }

  .product-div {
    border-width: 1px;
    border-style: solid;
    border-color: $divBorderColor;
    border-image: initial;
    border-radius: 10px;
    padding: 25px 0 15px ;
    margin-top: 15px !important;
    background-color: $divBg;

    .product-logo {
      width: 100px;
      height: 100px;
    }
    .product-logo-div {
      background-color: $bg2;
      font-size: 36px;
      height: 100px;
      width: 100px;
      border-radius: 50px;
      -webkit-box-align: center;
      align-items: center;
      display: flex;
      -webkit-box-pack: center;
      justify-content: center;
      box-shadow: $btnBoxShadow-sushi 4px 4px 8px inset, $btnBoxShadow-sushi-2 -6px -6px 12px inset;
      margin: 0 auto 16px;
    }

    .pdt-name {
      font-family: $fontTit;
      font-size: 24px;
      font-weight: 700;
      color: $titColor;
    }

    .pdt-txt {
      font-family: $txtColor;
      font-size: 16px;
      font-weight: 400;
      color: $txtColor;
    }

    .btn-select {
      -webkit-box-align: center;
      align-items:center;
      background-color: $walletBtnBg;
      border: 0;
      border-radius: 5px;
      box-shadow: $btnBoxShadow-sushi 2px 2px 4px, $btnBoxShadow-sushi-2 -2px -2px 4px -1px;
      color: $walletBtnColor;
      cursor: pointer;
      display: flex;
      font-size: 16px;
      font-weight: 700;
      height: 56px;
      -webkit-box-pack: center;
      justify-content: center;
      outline: none;
      padding-left: 24px;
      padding-right: 24px;
      width: 100%;
      margin: 20px 0 !important;
      outline: $walletBtnOutline;
    }
    .btn-select:hover,
    .btn-select:focus,
    .btn-select:active {
      border-width: 1px;
      border-style: solid;
      border-color: $walletBtnColor;
      border-image: initial;
      border-radius: 10px;
      outline: $walletBtnOutline;
      color: $walletBtnColor;
      background-color: $walletBtnBg;
    }

    .pdt-apy {
      background-color: $btnFontColor;
      color: $txtColor;
      border-width: 1px;
      border-style: solid;
      border-color: $divBorderColor;
      border-image: initial;
      border-radius: 3px;
      font-size: 12px;
      padding: 5px;
    }
  }

  .btn-lr-l {
    padding-right: 7px;
  }
  .btn-lr-r {
    padding-left: 7px;
  }

  .d-wallet-btn {
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
    margin: 45px 0 !important;
  }
  .d-wallet-btn:hover,
  .d-wallet-btn:focus,
  .d-wallet-btn:active {
    border-width: 2px;
    border-style: solid;
    border-color: $walletBtnBg;
    border-image: initial;
    border-radius: 15px;
    outline: $walletBtnOutline;
    color: $walletBtnBg;
    background-color: $walletBtnColor;
  }
  .d-wallet-btn-name {
    margin: 0 0 0 16px;
    letter-spacing: 2px;
    font-family: $fontTit;
    text-transform: capitalize;
    font-size: 18px;
  }
  .d-wallet-btn-icon {
    height: 40px;
    width: 40px;
  }

  .modal-dialog {
    margin-top: 12% !important;
  }
  .modal-body {
    background-color: $bg;
    padding: 30px 0;

    .modal-tit {
      font-size: 21px;
      color: $fontColor;
      letter-spacing: 2px;
      font-family: $fontTit;
      margin: 15px 0;
    }

    .modal-logo {
      width: 120px;
      height: 120px;
    }

    .balance-tit,
    .balance-txt {
      font-family: $fontTxt;
      color: $fontColor;
      font-size: 12px;
    }
    .balance-txt {
      color: $fontColor;
    }
    .btn-balance {
      -webkit-box-align: center;
      align-items:center;
      background-color: $btnBg2;
      border-width: 1px;
      border-style: solid;
      border-color: $divBorderColor;
      border-image: initial;
      transition: all 0.1s ease 0s;
      outline: $walletBtnOutline;
      border-radius: 9px;
      color: $bg2;
      cursor: pointer;
      display: flex;
      font-size: 12px;
      font-weight: 700;
      height: 18px;
      -webkit-box-pack: center;
      justify-content: center;
      outline: none;
      padding: 0;
      width: 35%;
      float: right;
    }
    .btn-balance:hover,
    .btn-balance:focus,
    .btn-balance:active {
      border-width: 1px;
      border-style: solid;
      border-color: $walletBtnBg;
      border-image: initial;
      border-radius: 9px;
      outline: $walletBtnOutline;
      color: $walletBtnBg;
      background-color: $walletBtnColor;
    }

    .staking-input {
      color: $titColor;
      background-color: $bg2;
      font-family: $fontTxt;
      width: 100%;
      height: 54px;
      font-size: 24px;
      padding: 16px;
      border-width: 1px;
      border-style: solid;
      border-color: $inputBorder;
      border-image: initial;
      border-radius: 15px;
      outline: none;
      margin-top: 25px !important;
      margin-bottom: 15px !important;
    }

    .btn-secondary {
      -webkit-box-align: center;
      align-items:center;
      background-color: $walletBtnBg;
      border-radius: 15px;
      border-width: 2px;
      border-style: solid;
      border-color: $divBorderColor;
      border-image: initial;
      transition: all 0.1s ease 0s;
      outline: $walletBtnOutline;
      box-shadow: $btnBoxShadow-sushi 2px 2px 4px, $btnBoxShadow-sushi-2 -2px -2px 4px -1px;
      color: $walletBtnColor;
      cursor: pointer;
      display: flex;
      font-size: 16px;
      font-weight: 700;
      height: 56px;
      -webkit-box-pack: center;
      justify-content: center;
      outline: none;
      padding-left: 24px;
      padding-right: 24px;
      width: 100%;
      margin: 30px 0 15px !important;
    }
    .btn-secondary:hover,
    .btn-secondary:focus,
    .btn-secondary:active {
      border-width: 2px;
      border-style: solid;
      border-color: $walletBtnBg;
      border-image: initial;
      border-radius: 15px;
      outline: $walletBtnOutline;
      color: $walletBtnBg;
      background-color: $walletBtnColor;
    }
  }
}
</style>
