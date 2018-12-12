<!--销售订单收款记录-->
<template>
<div>
    <div>
        <div class="panel panel-inverse" style="margin-top: 10px">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button class="btn btn-info btn-xs" v-on:click="createReceiveable" v-bind:disabled="checkState('PAYED')">新增收款记录</button>
                    <button class="btn btn-info btn-xs" v-on:click="done" v-bind:disabled="checkState('PAYED')">完成</button>
                </div>
                <h4 class="panel-title">收款记录</h4>
            </div>
            <div class="panel-body auto-height">
                <table id="receiveableTable"></table>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddReceivables">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增收款记录</h4>
                </div>
                <form @submit.prevent="addReceivablesRequest" id="form">
                    <div class="modal-body row">
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>金额</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.sale_receivables_amount" data-parsley-type="number" data-parsley-required="true" data-parsley-range="[1, 9999999]">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>收款人</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" style="width:100%" id="sale_receivables_operator_id" data-parsley-required="true"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>支付方式</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" style="width:100%" id="sale_receivables_pay_type" data-parsley-required="true"></select>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCASOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            selectState: 2,
        }
    },
    name: 'NCASReceiveableControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {
            let _self = this;
            _self.initTable();
            $('#form').parsley()
            common.initSelect2($('#sale_receivables_operator_id'), _self.pagePara.employeeInfo)
            common.initSelect2($('#sale_receivables_pay_type'), _self.pagePara.payTypeInfo)
        },
        order: function() {
          $('#receiveableTable').bootstrapTable("refresh");
        }
    },
    methods: {
        search: function() {
            let _self = this
            $('#receiveableTable').bootstrapTable("refresh")
        },
        createReceiveable: function () {
            $('#AddReceivables').modal('show');
        },
        done: function () {
          let _self = this
          let params = {
            orderId: _self.order.order_id
          }
          _self.$http.post(apiUrl + 'confirm_receiveable', params).then((response) => {
            let retData = response.data.info
            _self.$parent.refreshOrder();
          }, (response) => {
            common.dealErrorCommon(_self, response)
          });
        },
        initTable: function () {
          let _self = this;
          let $table = $('#receiveableTable');

          function queryParams(params) {
            params.order_id = _self.order.order_id;
            return JSON.stringify(params);
          }

          $table.bootstrapTable({
            method: 'POST',
            url: apiUrl + 'search_sale_receiveable',
            queryParams: queryParams,
            sidePagination: 'server',
            ajaxOptions: common.bootstrapTableAjaxOtions,
            responseHandler: function(res) {
              return res.info;
            },
            height: common.getTableHeight(),
            columns: [
              common.BTRowFormatWithIndex('NO.'),
              common.BTRowFormatWithFormatterAlignRight('sale_receivables_amount', '金额',common.priceFormat),
              common.BTRowFormatEdSelect2(_self, 'sale_receivables_pay_type', '支付方式', 'payTypeInfo'),
              common.BTRowFormatEdSelectAlignLeft(_self, 'sale_receivables_operator_id', '收款人', 'employeeInfo'),
              common.BTRowFormat('created_at', '创建日期')
            ],
            idField: 'sale_receivables_id',
            uniqueId: 'sale_receivables_id',
            pagination: true,
            pageSize: common.pageSize(),
            pageList: [10, 15, 25, 50, 100],
            locale: 'zh-CN',
            onEditableShown: function(field, row, $el, editable) {
              _self.oldRow = $.extend(true, {}, row)
            },
            onEditableSave: function(field, row, oldValue, $el) {
              _self.$http.post(apiUrl + 'modify_sale_receiveable', {
                'old': _self.oldRow,
                'new': row
              }).then((response) => {
                $('#receiveableTable').bootstrapTable("refresh")
              }, (response) => {
                common.dealErrorCommon(this, response)
              })
            },
            onPostBody: function() {
              $('[data-name="sale_receivables_amount"]').each(function() {
                let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                if (_self.checkState('PAYED')) {
                  $(this).editable({
                    type: 'text',
                    emptytext: '无',
                    disabled: true
                  })
                }
              })
              $('[data-name="sale_receivables_pay_type"]').each(function() {
                let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                if (_self.checkState('PAYED')) {
                  $(this).editable({
                    type: 'select2',
                    emptytext: '无',
                    source: _self.pagePara['payTypeInfo'],
                    disabled: true
                  })
                }
              })
              $('[data-name="sale_receivables_operator_id"]').each(function() {
                let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                if (_self.checkState('PAYED')) {
                  $(this).editable({
                    type: 'select2',
                    emptytext: '无',
                    source: _self.pagePara['employeeInfo'],
                    disabled: true
                  })
                }
              })
            },
          })
          common.changeTableClass($table)
        },
      addReceivablesRequest: function() {
        let _self = this;
        let $form = $('#form')
        if ($form.parsley().isValid()) {
          try {
            _self.workRow.order_id = _self.order.order_id
            _self.workRow.sale_receivables_pay_type = common.getSelect2Val('sale_receivables_pay_type')
            _self.workRow.sale_receivables_operator_id = common.getSelect2Val('sale_receivables_operator_id')
            _self.$http.post(apiUrl + 'add_sale_receiveable', _self.workRow).then((response) => {
              let retData = response.data.info
              $form.parsley().reset();
              $('#AddReceivables').modal('hide');
              $('#receiveableTable').bootstrapTable("refresh")
            }, (response) => {
              common.dealErrorCommon(_self, response)
            });
          } catch (error) {
            common.dealErrorCommon(_self, error)
          }
        }
      },
      checkState: function(state, event) {
        let _self = this
        if (_self.order.statedict) {
          if (state in _self.order.statedict) return true
        }
        return false
      }
    }
}
</script>
<style scoped>
</style>
