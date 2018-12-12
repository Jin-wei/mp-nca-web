<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">订单评审</a></li>
            <li class="active">订单评审详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <span>订单号: {{orderId}}</span>
                        <span style="float: right">产品规划状态: {{proudctPlanState}}</span>
                    </div>
                    <div class="panel-body">
                        <table id="reviewTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" @click="createDelivery">添加交货</button>
                        </div>
                        <h4 class="panel-title">交货批次列表</h4>
                    </div>
                    <div class="panel-body">
                        <table id="deliveryTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="deliveryModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">添加交货批次</h4>
                    </div>
                    <form @submit.prevent="addDelivery" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-10" style="margin-bottom: 0px">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>交货日期</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="deliveryDate" data-parsley-required="true" autocomplete="off">
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc')
  const apiUrl = '/api/nca/ordermanage/NCAOrderReviewControl?method='

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: '',
        orderId: '',
        proudctPlanState: ''
      }
    },
    name: 'NCAOrderReviewDetailControl',
    mounted: function() {
      let _self = this
      _self.orderId = _self.$route.query.orderId;
      let $reviewTable = $('#reviewTable')
      let $deliveryTable = $('#deliveryTable')

      function nameFormatter(value, row) {
        return [
          '<a class="show_detail" style="cursor: pointer;">',
          '查看',
          '</a>'
        ].join('')
      }
      function stateFormatter(value, row) {
        if (value == 1) {
          return [
            '<a class="confirm_delivery" style="cursor: pointer;">',
            '确认交货',
            '</a>'
          ].join('')
        } else if (value == 2) {
          return [
            '<span>已交货</span>'
          ].join('')
        } else if (value == 3) {
          return [
            '<span>已超时</span>'
          ].join('')
        }
      }
      function compareDate(d1,d2) {
        let t1 = new Date(d1.replace(/-/g,"\/")).getTime()
        let t2 = new Date(d2.replace(/-/g,"\/")).getTime()
        let days = parseInt((t1-t2)/(1000 * 60 * 60 * 24))
        return days > 0
      }

      window.tableEvents = {
        'click .show_detail': function(e, value, row, index) {
          _self.$router.push({
            path: '/nca/ordermanage/NCADeliveryDetailControl',
            query: {
              deliveryId: row.delivery_id,
              orderId: _self.orderId
            }
          })
        },
        'click .confirm_delivery': async function(e, value, row, index) {
          let params = {
            delivery_id: row.delivery_id,
            delivery_state: 2,
            delivery_remark: row.delivery_remark,
            delivery_date: row.delivery_date
          }
          let response = await _self.$http.post(apiUrl + 'modify_delivery', params)
          let retData = response.data.info
          $('#deliveryTable').bootstrapTable("refresh")
        }
      };
      //评审内容
      function initReviewTable() {
        $reviewTable.bootstrapTable({
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatAlignLeft('require_name', '评审项目'),
            common.BTRowFormatWithFormatterAlignLeft('require_description', '评审责任描述',common.remarkFormatter),
            common.BTRowFormatEdSelectAlignLeft(_self, 'duty_user_id', '责任人', 'employeeInfo'),
            common.BTRowFormatWithFormatterAlignLeft('review_description', '评审提交内容',common.remarkFormatter),
            common.BTRowFormatEdSelect2Disabled(_self, 'review_status', '状态', 'orderReviewStatusInfo'),
            common.BTRowFormat('review_date', '评审日期')
          ],
          idField: 'require_id',
          uniqueId: 'require_id',
          clickToSelect: true,
          locale: 'zh-CN',
          onEditableShown: function(field, row, $el, editable) {
//                        _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: function(field, row, oldValue, $el) {
            let param = {
              reviewId: row.review_id,
              dutyUserId:row.duty_user_id,
              order_id: _self.orderId
            }
            _self.$http.post(apiUrl + 'set_order_review_duty', param).then((response) => {
            }, (response) => {
              common.dealErrorCommon(_self, response)
            })
          },
          onPostBody: function() {
            $('[data-toggle="popover"]').each(function () {
              $(this).popover()
            })
          }
        })
        common.changeTableClass($reviewTable)
      }

      function queryParams(params) {
        params.orderId = _self.orderId
        return JSON.stringify(params)
      }

      //交货列表
      function initDeliveryTable() {
        $deliveryTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_delivery',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('', '交货内容',nameFormatter, tableEvents),
            common.BTRowFormatEditableDatePicker('delivery_date', '交货日期'),
            common.BTRowFormatEdTextareaWidthAlignLeft('delivery_remark', '备注',150,10),
            common.BTRowFormatWithFE('delivery_state', '状态',stateFormatter, tableEvents)
          ],
          idField: 'delivery_id',
          uniqueId: 'delivery_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: (field, row, $el, editable) => {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: async (field, row, oldValue, $el) => {
            let result = compareDate(new Date().toString(), row.delivery_date)
            let params = {
              delivery_id: row.delivery_id,
              delivery_state: result ? 3 : 1,
              delivery_remark: row.delivery_remark,
              delivery_date: row.delivery_date
            }
            let response = await _self.$http.post(apiUrl + 'modify_delivery', params)
            let retData = response.data.info
            $('#deliveryTable').bootstrapTable("refresh")
          },
          onPostBody: function() {
            $("[data-uniqueid]").each(function() {
              let actrow = $('#deliveryTable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-uniqueid'))
              if (actrow && actrow.delivery_state) {
                if (actrow.delivery_state == 2) {
                  $(this).find('[data-name]').each(function() {
                    $(this).attr("data-disabled", true)
                  })
                }
              }
            })
          }
        })
        common.changeTableClass($deliveryTable)
      }

      async function getInitData () {
        let response = await _self.$http.post(apiUrl + 'init', {})
        let retData = response.data.info
        _self.pagePara = JSON.parse(JSON.stringify(retData))
      }

      async function getOrderRequire () {
        let params = {
          orderId: _self.orderId
        }
        let response = await _self.$http.post(apiUrl + 'search_order_require', params)
        let retData = response.data.info
        $reviewTable.bootstrapTable('load', {
          data: retData
        })
      }
      async function getProductPlanState () {
        let params = {
          orderId: _self.orderId
        }
        let response = await _self.$http.post(apiUrl + 'get_product_plan_state', params)
        let retData = response.data.info
        if (retData.valid_state == 0 || !retData.valid_state) {
          _self.proudctPlanState = '未评审'
        } else if (retData.valid_state == 1) {
          _self.proudctPlanState = '部分评审'
        } else if (retData.valid_state == 2) {
          _self.proudctPlanState = '全部评审'
        }
      }

      async function initPage() {
        await getInitData()
        initReviewTable()
        initDeliveryTable()
        await getOrderRequire()
        await getProductPlanState()
        common.initDatepicker($('#deliveryDate'))
      }

      $(function() {
        initPage()
      })
    },
    methods: {
      createDelivery: function () {
        $('#deliveryModal').modal('show')
      },
      addDelivery: async function () {
        let _self = this
        if ($('#formA').parsley().isValid()) {
          try {
            let params = {
              orderId: _self.orderId,
              deliveryRemark: '',
              deliveryDate: $('#deliveryDate').val()
            }
            let response = await _self.$http.post(apiUrl + 'add_delivery', params)
            let retData = response.data.info
            if (response.data.errno == 0) {
              common.dealSuccessCommon('交货批次增加成功')
              $('#deliveryModal').modal('hide')
              $('#deliveryTable').bootstrapTable("refresh")
            }
          } catch (error) {
            common.dealErrorCommon(_self, error)
          }
        }
      }
    }
  }
</script>
<style scoped>
</style>
