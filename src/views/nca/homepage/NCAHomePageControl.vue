<template>
  <div class="h-container h-container-1">
    <!--<div>-->
      <!--<over-view></over-view>-->
    <!--</div>-->
    <div style="margin-top: 28px;">
      <img v-show="tenant==='nca'" src="/static/images/hq-home.jpg" style="width: 100%;max-height: 300px;" alt="" />
    </div>
    <div class="top-margin">
        <div  class="col-sm-7">
            <div>
                <notification v-on:more="listenMessage"></notification>
            </div>
            <div  style="margin-top: 18px;" v-show="showDegsin() && tenant==='nca'">
                <div class="row border-radius a-container">
                    <div class="a-header main-title-size">
                        <span>设计方案</span>
                    </div>
                    <div>
                        <div class="media">
                            <a class="media-left" href="javascript:;">
                                <img src="/static/images/home-degsin.jpg" alt="" class="media-object">
                            </a>
                            <div class="media-body">
                                <div class="design-div">
                                    <h1>设计师请进
                                        <router-link tag="a" target="_blank" to="/nca/NCAOrderSearchControl">
                                        <span class="fa-stack fa-1x text-primary">
                                            <i class="fa fa-square-o fa-stack-2x"></i>
                                            <i class="fa fa-sign-in fa-stack-1x"></i>
                                        </span>
                                        </router-link>
                                    </h1>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

      <div class="col-sm-5 left-padding">
        <active-order></active-order>
      </div>

    </div>
  </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = ''

//    import overView from './NCAOverView.vue'
    import notification from './NCANotification.vue'
    import activeOrder from './NCAToDoList.vue'

    export default {
        data: function() {
            return {
                tenant:common.getStoreData('tenant')
            }
        },
        name: 'homePageControl',
        components: {
//            overView,
            notification,
            activeOrder
        },
        mounted: function() {
            let _self = this

            function initPage() {
                common.reSizeCall()
            }

            initPage()
        },
        methods: {
            showDegsin: function(event) {
              let userinfo = common.getStoreData('userinfo')
              return common.checkAuth('NCAORDERSEARCHCONTROL', userinfo.menulist)
            },
            listenMessage: function(message) {
                alert(message)
            }
        }
    }
</script>
<style scoped>
  .h-container {
    /*padding-top: 30px;*/
    /*padding-left: 20px;*/
    /*padding-right: 20px;*/
    /*padding-top:28px;*/
  }

  .navigation-title {
    padding-left: 5px;
    padding-bottom: 20px;
    font-size: 14px;
    font-weight: bold;
  }

  .top-margin {
    margin-top: 20px;
  }

  .left-padding {
    padding-left: 20px;
  }

  .a-container {
    background: white;
    padding-left: 20px;
    padding-right: 20px;
    padding-bottom: 25px;
  }

  .a-header {
    border-bottom-style: solid;
    border-bottom-color: grey;
    border-bottom-width: 1px;
    padding-top: 20px;
    padding-bottom: 10px;
    font-weight: bold;
  }

  .a-row {
    border-bottom-style: dashed;
    border-bottom-color: grey;
    border-bottom-width: 1px;
    margin-left: 1px;
    margin-right: 1px;
    padding-top: 10px;
    padding-bottom: 10px;
  }

  .a-row .left-margin {
    padding-left: 0px;
  }

  .a-row .top-margin {
    margin-top: 5px;
  }

  .a-title {
    /*width: 100px;*/
    display: inline-block;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    position: relative;
    top: 5px;
  }
  .design-div{
      text-align: left;
  }
</style>
