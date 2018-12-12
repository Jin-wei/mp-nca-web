<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单评审详情</a></li>
            <li class="active">交货列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" @click="addDelivery">添加交货物料</button>
                        </div>
                        <h4 class="panel-title">交货列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索物料编码、物料名称" style="width: 210px;">
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="search()">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="panel-body">
                            <table id="deliveryItemTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">添加交货物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3">
                            <input class="form-control" id="search_material" placeholder="搜索物料编码、名称" v-on:input="getMateriel">
                        </div>
                        <div class="form-group col-xs-3">
                            <button type="button" class="btn btn-info" v-on:click="addMaterial"><i class="fa fa-fw fa-plus"></i>增加</button>
                        </div>
                        <div class="panel-body auto-height">
                            <table id="table"></table>
                        </div>
                    </div>
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
        deliveryId: '',
        searchedRow: []
      }
    },
    name: 'NCADeliveryDetailControl',
    mounted: function() {
      let _self = this
      let $deliveryItemTable = $('#deliveryItemTable')
      _self.orderId = _self.$route.query.orderId;
      _self.deliveryId = _self.$route.query.deliveryId;
      let $table = $('#table')

      function queryParams(params) {
        params.delivery_id = _self.deliveryId
        params.search_text = $('#search_text').val()
        return JSON.stringify(params);
      }

      function operateFormatter(value, row) {
        return [
          '<a class="delete_delivery" style="cursor: pointer;">',
          '删除',
          '</a>'
        ].join('')
      }

      window.tableEvents = {
        'click .delete_delivery': function(e, value, row, index) {
          common.dealConfrimCommon('确定要删除交货物料', function() {
            let params = {
              deliveryitem_id: row.deliveryitem_id
            }
            _self.$http.post(apiUrl + 'delete_delivery_item', params).then((response) => {
              common.dealSuccessCommon('删除成功')
              $('#deliveryItemTable').bootstrapTable("refresh")
            }, (response) => {
              common.dealErrorCommon(_self, response)
            })
          })
        }
      };

      function initDeliveryItemTable() {
        $deliveryItemTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_delivery_item',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormat('materiel_code', '物料编码'),
            common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
            common.BTRowFormat('materiel_format', '规格型号'),
            common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
            common.BTRowFormatEditableRight('delivery_item_number', '数量'),
            common.BTRowFormatWithFE('order_id', '操作',operateFormatter, tableEvents)
          ],
          idField: 'deliveryitem_id',
          uniqueId: 'deliveryitem_id',
          clickToSelect: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: (field, row, $el, editable) => {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: async (field, row, oldValue, $el) => {
            let response = await _self.$http.post(apiUrl + 'modify_delivery_item', row)
            let retData = response.data.info
            $deliveryItemTable.bootstrapTable("refresh")
          }
        })
        common.changeTableClass($deliveryItemTable)
      }

        function queryParamsTable(params) {
        params.search_material = $('#search_material').val()
        params.order_id = _self.orderId
        return JSON.stringify(params);
      }

      function initTable() {
        $table.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_order_material',
          queryParams: queryParamsTable,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [{
            field: '',
            checkbox: true
          },
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatWithFE('materiel_code', '物料编码'),
            common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
            common.BTRowFormat('materiel_format', '规格型号'),
            common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
            common.BTRowFormatAlignRight('materiel_amount', '订单数量')
          ],
          idField: 'ordermateriel_id',
          uniqueId: 'ordermateriel_id',
          striped: true,
          pagination: true,
          pageSize: common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onCheckAll: function(rows) {
            for (let i = 0; i < rows.length; i++) {
              _self.searchedRow.push(rows[i])
            }
          },
          onUncheckAll: function(rows) {
            _self.searchedRow = []
          },
          onCheck: function(row, $element) {
            _self.searchedRow.push(row)
          },
          onUncheck: function(row, $element) {
            _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
          },
          onPageChange: function(number, size) {
            _self.searchedRow = []
          }
        });
        common.changeTableClass($table)
      }

      async function getInitData () {
        let response = await _self.$http.post(apiUrl + 'init', {})
        let retData = response.data.info
        _self.pagePara = JSON.parse(JSON.stringify(retData))
      }

      async function initPage() {
        await getInitData()
        initDeliveryItemTable()
        initTable()
      }

      $(function() {
        initPage()
      })
    },
    methods: {
      getMateriel: function(event){
        let _self = this;
        $('#table').bootstrapTable("refresh")
      },
      search: function() {
        let _self = this
        $('#deliveryItemTable').bootstrapTable("refresh")
      },
      addDelivery: function () {
        $('#AddMaterielModal').modal('show')
      },
      addMaterial: async function () {
        let _self = this
        let params = {
          delivery_id: _self.deliveryId,
          materials: _self.searchedRow
        }
        let response = await _self.$http.post(apiUrl + 'add_delivery_item', params)
        let retData = response.data.info
        $('#AddMaterielModal').modal('hide')
        $('#deliveryItemTable').bootstrapTable("refresh")
      }
    }
  }
</script>
<style scoped>
</style>