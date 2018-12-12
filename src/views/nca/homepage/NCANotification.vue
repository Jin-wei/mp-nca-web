<template>
  <div class="row border-radius a-container">
    <div class="a-header main-title-size">
      <span>通知消息</span>
    </div>
    <div>
      <div v-for="t in orderList" class="row a-row">
        <div class="col-md-12 no-padding">
          <div class="col-sm-6 left-margin">
            <!--<span>{{t.orderName}}</span>-->
              <span class="a-title"><a style="width: 100px" href="#" @click="showTodoDetail(t.message_id,t.message_user_title,t.message_user_type)">{{t.message_user_title}}</a></span>
          </div>
          <div class="col-sm-6 left-margin">
            <span>创建日期：</span>
            <span>{{t.message_start_date}}</span>
          </div>
        </div>
        <div class="col-md-12 no-padding">
          <div class="col-sm-6 left-margin">
            <span>创建人：{{t.username}}</span>
            <span></span>
          </div>
          <div class="col-sm-6 left-margin">
            <span>状态：</span>
            <span class="a-title">{{t.message_user_state}}</span>
            <!--<a style="width: 100px" v-for="(data,index) in pagePara" :key="data.id">{{data.text}}</a>-->
          </div>
        </div>
      </div>
      <div class="pull-right more">
        <a href="#" @click="clickMore">查看全部>></a>
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
                pagePara: {},
                orderList: [],
                userinfo: common.getStoreData('userinfo')
            }
        },
        mounted: function() {
            let _self = this

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            function getNoticeOrder() {
                _self.$http.post(apiUrl + 'getNoitceOrder', {user_id: _self.userinfo.user_id}).then((response) => {
                    let retData = response.data.info;
                    _self.orderList = retData.rows.slice(0, 5);
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            async function initPage() {
                await getInit()
                getNoticeOrder()
                common.reSizeCall()
            }

            initPage()
        },
        methods: {
            showTodoDetail: function(message_id,message_user_title,message_user_type) {
                let _self = this;
                let  tt = (message_user_title).search("公") == 0;
                if (tt) {
                    let notice_id = message_id;
                    let url = '/nca/baseconfig/NCANoticeReceiveDetailControl?notice_id=' + notice_id;
                    _self.$router.push({
                        path: url
                    })
                } else {
                    if (message_user_type == 8 ){
                        let url = '/nca/ordermanage/NCASOrderDetailControl?orderId=' + message_id;
                        _self.$router.push({
                            path: url
                        })
                    } else {
                        let url = '/nca/ordermanage/NCAOrderDetailControl?orderId=' + message_id;
                        _self.$router.push({
                            path: url
                        })
                    }
                }
            },
            clickMore : function () {
                let _self = this
                let url = '/nca/baseconfig/NCAMessageListControl';
                _self.$router.push({
                    path: url
                })
            }
        }
    }
</script>
<style scoped>
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
    vertical-align: top;
  }
  .more {
    margin-top: 10px;
  }
  .no-padding{
    padding: 0;
  }
</style>
