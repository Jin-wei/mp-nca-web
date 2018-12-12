<template>
  <div class="row font-name ov-container">
    <div class="col-lg-3 col-xs-6">
      <div class="box-container color1 border-radius">
        <div class="box-inner">
          <div class="col-xs-7">
            <p class="dec">总客户数</p>
            <p class="count">{{totalCustomer}}</p>
          </div>
          <div class="col-xs-5 icon">
            <i class="fa icon-users fa-3x" style="opacity:0.5;font-weight: bold;"></i>
          </div>
        </div>
        <div class="footer">
        </div>
      </div>
    </div>
    <div class="col-lg-3 col-xs-6">
      <div class="box-container color2 border-radius">
        <div class="box-inner">
          <div class="col-xs-7">
            <p class="dec">本月新增客户数</p>
            <p class="count">{{increasedCustomer}}</p>
          </div>
          <div class="col-xs-5 icon">
            <i class="fa icon-user-follow fa-3x" style="opacity:0.5;font-weight: bold;"></i>
          </div>
        </div>
        <div class="footer">
        </div>
      </div>
    </div>
    <div class="col-lg-3 col-xs-6">
      <div class="box-container color3 border-radius">
        <div class="box-inner">
          <div class="col-xs-7">
            <p class="dec">活跃订单数</p>
            <p class="count">{{activeOrder}}</p>
          </div>
          <div class="col-xs-5 icon">
            <i class="fa icon-docs fa-3x" style="opacity:0.5;font-weight: bold;"></i>
          </div>
        </div>
        <div class="footer">
        </div>
      </div>
    </div>
    <div class="col-lg-3 col-xs-6">
      <div class="box-container color4 border-radius">
        <div class="box-inner">
          <div class="col-xs-7">
            <p class="dec">本月营业额</p>
            <p class="count">￥{{salesAmount}}</p>
          </div>
          <div class="col-xs-5 icon">
            <i class="fa icon-diamond fa-3x" style="opacity:0.5;font-weight: bold;"></i>
          </div>
        </div>
        <div class="footer">
        </div>
      </div>
    </div>
  </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/homepage/NCAHomePageControl?method='

    export default {
        data: function() {
            return {
                totalCustomer: 0,
                increasedCustomer: 0,
                activeOrder: 0,
                salesAmount: 0,
                userinfo: common.getStoreData('userinfo')
            }
        },
        mounted: function() {
            let _self = this

            function getTotalCustomer() {
                _self.$http.post(apiUrl + 'getTotalCustomer', {domain_id: _self.userinfo.domain_id}).then((response) => {
                    let retData = response.data.info
                    _self.totalCustomer = retData.totalCustomer
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            function getIncreasedCustomer() {
                _self.$http.post(apiUrl + 'getIncreasedCustomer', {domain_id: _self.userinfo.domain_id}).then((response) => {
                    let retData = response.data.info
                    _self.increasedCustomer = retData.increasedCustomer
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            function getActiveOrder() {
                _self.$http.post(apiUrl + 'getActiveOrder', {domain_id: _self.userinfo.domain_id}).then((response) => {
                    let retData = response.data.info
                    _self.activeOrder = retData.orderCount
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            function getSalesAmount() {
                _self.$http.post(apiUrl + 'getSalesAmount', {domain_id: _self.userinfo.domain_id}).then((response) => {
                    let retData = response.data.info
                    _self.salesAmount = retData.salesAmount
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            function initPage() {
                getTotalCustomer()
                getIncreasedCustomer()
                getActiveOrder()
                getSalesAmount()
                common.reSizeCall()
            }

            initPage()

        },
        methods: {}
    }
</script>
<style scoped>
  .ov-container {
    padding-left: 5px;
  }

  .box-container {
    height: 110px;
    overflow: hidden;
    color: white;
    /*margin-right: 10px;*/
  }

  .box-inner {
    padding-top: 15px;
    margin-left: 10px;
  }

  .box-inner .dec {
    margin-top: 5px;
    margin-bottom: 0px;
    font-size: 12px;
  }

  .box-inner .count {
    margin-top: 5px;
    margin-bottom: 0px;
    font-size: 16px;
    font-weight: bold;
  }

  .box-inner .icon {
    padding-left: 40px;
    padding-top: 5px;
  }

  .box-container .footer {
    border-top-width: 0px;
    margin-top: 65px;
    height: 30px;
    margin-left: 0px;
    margin-right: 0px;
    background: rgba(0, 0, 0, 0.15);
  }

  .color1 {
    background: #00acac;
  }

  .color2 {
    background: #348fe2;
  }

  .color3 {
    background: #727cb6;
  }

  .color4 {
    background: #ff5b57;
  }
</style>
