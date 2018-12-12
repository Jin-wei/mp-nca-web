<template>
  <div class="row border-radius a-container">
    <div class="a-header main-title-size">
      <span>待办事项</span>
    </div>
    <div>
      <div v-for="t in todoList" class="row a-row">
        <div class="col-md-12 no-padding">
          <div class="col-sm-6 left-margin">
            <span>任务单号：</span>
            <span class="a-title"><a style="width: 100px" href="#" @click="showTodoDetail(t.task_id)">{{t.task_id}}</a></span>
          </div>
          <div class="col-sm-6 left-margin">
            <span>创建日期：</span>
            <span>{{t.task_create_date}}</span>
          </div>
        </div>
        <div>
          <div class="col-sm-6 left-margin">
            <span>创建人：{{t.username}}</span>
            <span></span>
          </div>
        </div>
        <div>
          <div class="col-sm-6 left-margin">
            <span>状态：</span>
            <span class="a-title"><a style="width: 100px" href="#" @click="showTodoDetail(t.task_id)">{{t.task_type |stateFilter }}</a></span>
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
                todoList: [],
                userinfo: common.getStoreData('userinfo')
            }
        },
        mounted: function() {
            let _self = this

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info.taskTypeInfo
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            function getLatestOrder() {
                _self.$http.post(apiUrl + 'getLatestOrder', {user_id: _self.userinfo.user_id}).then((response) => {
                    let retData = response.data.info
                    _self.todoList = retData.latestOrder.slice(0, 5)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
 
            async function initPage() {
                await getInit()
                getLatestOrder()
                common.reSizeCall()
            }

            initPage()
        },
        methods: {
            showTodoDetail: function(taskId) {
                let _self = this
                let url = '/nca/baseconfig/NCATaskDetailControl?taskId=' + taskId;
                _self.$router.push({
                    path: url
                })
            },
            clickMore : function () {
                let _self = this
                let url = '/nca/baseconfig/NCATaskListControl';
                _self.$router.push({
                    path: url
                })
            }
        },
        filters: {
            stateFilter: function (value) {
                if (value === '1'){
                    return '一般任务'
                } else if (value === '2') {
                    return '采购申请'
                } else if (value === '3') {
                    return '内部审核'
                } else if (value === '4') {
                    return '生产计划'
                } else if (value === '5') {
                    return '订单评审'
                } else if (value === '6') {
                    return '订单验收'
                } else if (value === '7') {
                    return '物料审核'
                } else if (value === '8') {
                    return '物料变更'
                } else if (value === '9') {
                    return '报废审核'
                } else if (value === '10') {
                    return '入库申请'
                } else if (value === '11') {
                    return '出库申请'
                } else if (value === '12') {
                    return '预算审核'
                } else if (value === '13') {
                    return '决算审核'
                } else if (value === '14') {
                    return '公告通知'
                } else if (value === '15') {
                    return '招录任务'
                } else if (value === '16') {
                    return '退货任务'
                } else if (value === '17') {
                    return '闲置库存申请'
                } else if (value === '18') {
                    return '会议通知'
                } else if (value === '19') {
                    return '会议跟进事项'
                } else if (value === '20') {
                    return '会议通知'
                } else if (value === '21') {
                    return '会议通知'
                } else if (value === '22') {
                    return '交通接待申请通知'
                } else if (value === '23') {
                    return '会议通知'
                } else if (value === '24') {
                    return '交通接待报销申请通知'
                } else if (value === '25') {
                    return '文控管理'
                } else if (value === '26') {
                    return '文件发布通知'
                } else if (value === '27') {
                    return '请假通知'
                } else if (value === '28') {
                    return '资金支出管理通知'
                } else if (value === '29') {
                    return '固定资产申购'
                } else if (value === '30') {
                    return '固定资产验收'
                } else if (value === '31') {
                    return '固定资产维修'
                } else if (value === '32') {
                    return '新增待摊资产项目'
                } else if (value === '33') {
                    return '构建预算'
                } else if (value === '34') {
                    return '材料申购'
                } else if (value === '35') {
                    return '材料收料'
                } else if (value === '36') {
                    return '人工结算'
                } else if (value === '37') {
                    return '材料耗用'
                } else if (value === '38') {
                    return '构建费用'
                } else if (value === '39') {
                    return '资产报废'
                } else if (value === '40') {
                    return '低值易耗品申购'
                } else if (value === '41') {
                    return '低值易耗品验收申请'
                } else if (value === '42') {
                    return '待摊资产提交验收'
                } else if (value === '43') {
                    return '盘点审批任务'
                }  else if (value === '44') {
                    return '盘点完成消息通知'
                } else if (value === '45') {
                    return '收款申报'
                } else if (value === '46') {
                    return '付款确认'
                } else {
                    return '无'
                }
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
    /*top: 5px;*/
  }
  .more {
    margin-top: 10px;
  }
  .no-padding{
    padding: 0;
  }
</style>
