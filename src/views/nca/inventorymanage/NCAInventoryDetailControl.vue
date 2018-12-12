<!--库存收发明细-->
<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">库存管理</a></li>
            <li class="active">收发存数据列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收发存数据列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间"/>
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间"/>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="bill_code" placeholder="请输入单据编号" style="width: 136px"/>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="order_id" placeholder="请输入源单编号" style="width: 136px"/>
                                </div>
                                <div class="form-group">
                                    <input class="form-control" multiple id="search_text" placeholder="搜索对应单位、物料编码、物料名称" style="width: 258px;">
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="inventoryDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCAInventoryDetailControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: ''
      }
    },
    name: 'NCAInventoryDetailControl',
    mounted: function() {
      let _self = this;
      let $table = $('#inventoryDetailTable')

      function queryParams(params) {
        if($('#search_text').val()){
          params.search_text = $('#search_text').val()
        }
        if($('#bill_code').val()){
          params.bill_code = $('#bill_code').val()
        }
        if($('#order_id').val()){
          params.order_id = $('#order_id').val()
        }
        if($('#start_date').val()){
          params.created_at_start = $('#start_date').val()
        }
        if($('#end_date').val()){
          params.created_at_end = $('#end_date').val()
        }
        return JSON.stringify(params)
      }

      function unitFilter(value) {
        for (let i = 0; i < _self.pagePara.unitInfo.length; i++) {
          if (_self.pagePara.unitInfo[i].id === value) {
            return _self.pagePara.unitInfo[i].text
          }
        }
        return ''
      }

      function operateType(value) {
        for (let i = 0; i < _self.pagePara.inventoryOperateType.length; i++) {
          if (_self.pagePara.inventoryOperateType[i].id === value) {
            return _self.pagePara.inventoryOperateType[i].text
          }
        }
        return ''
      }

      function inAmountFilter(value, row, index) {
        if(row.account_operate_type == 1 || row.account_operate_type == 3) {
          return value
        } else {
          return '-'
        }
      }

      function outAmountFilter(value, row, index) {
        if(row.account_operate_type == 2 || row.account_operate_type == 4) {
          return value
        } else {
          return '-'
        }
      }

      function initTable() {
        $table.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
              common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormat('create_date', '操作日期'),
            common.BTRowFormat('company_name', '对应单位'),
            common.BTRowFormatWithFormatter('account_operate_type', '业务类型', operateType),
            common.BTRowFormat('bill_code', '单据编号'),
            common.BTRowFormat('order_id', '源单编号'),
            common.BTRowFormat('materiel_code', '物料编码'),
            common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
            common.BTRowFormat('materiel_format', '规格型号'),
            common.BTRowFormatWithFormatter('materiel_unit', '单位', unitFilter),
            common.BTRowFormatWithFormatterAlignRight('account_operate_amount', '入库数量', inAmountFilter),
            common.BTRowFormatWithFormatterAlignRight('account_operate_amount', '出库数量', outAmountFilter),
//            common.BTRowFormat('remain_amount', '结存数量')
          ],
          idField: 'inventoryaccount_id',
          uniqueId: 'inventoryaccount_id',
          striped: true,
          clickToSelect: true,
          pagination: true,
          pageSize:common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: function(field, row, $el, editable) {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: function(field, row, oldValue, $el) {
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'inventoryaccount_id', $table)
          }
        });
        common.changeTableClass($table)
      }

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        initTable()
      }

      $(async function() {
        await initPage()
        common.initDatepicker($('#start_date'));
        common.initDatepicker($('#end_date'));
      });
    },
    methods: {
      search: function(event) {
        let _self = this
        $('#inventoryDetailTable').bootstrapTable("refresh", _self.queryParams)
      }
    }
  }
</script>
<style scoped>
</style>
