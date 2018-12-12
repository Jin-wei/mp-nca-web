<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">库存管理</a></li>
            <li class="active">实时库存列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'实时库存列表'"></DomainChooseHeader>
                    <div class="panel-toolbar">
                        <div id="">
                            <div class="form-inline" role="form">
                                <div class="row">
                                    <div class="form-group">
                                        <div class="form-group" style="padding-left: 10px">
                                            <input class="form-control" multiple id="search_text" placeholder="搜索物料编码、物料名称、规格型号" style="width: 230px;">
                                        </div>
                                        <div class="form-group">
                                            <select class="select2 form-control select-width" id="warehouse_id" style="width: 100px" v-on:change="getWarehouseZone">
                                                <option value="">请选择仓库</option>
                                                <option v-for="warehouse in pagePara.warehousesInfo" v-bind:value="warehouse.id">{{warehouse.text}}</option>
                                            </select>
                                        </div>
                                        <div class="form-group">
                                            <select class="select2 form-control select-width" id="warehouse_zone_id" style="width: 100px">
                                                <option value="">请选择仓区</option>
                                                <option v-for="zone in pagePara.warehouseZoneInfo" v-bind:value="zone.id">{{zone.text}}</option>
                                            </select>
                                        </div>
                                        <div class="form-group">
                                            <select class="select2 form-control select-width" id="is_idle_stock" style="width: 120px">
                                                <option value="">是否闲置库存</option>
                                                <option v-for="tf in pagePara.tfInfo" v-bind:value="tf.id">{{tf.text}}</option>
                                            </select>
                                        </div>
                                        <div class="form-group">
                                            <select class="select2 form-control select-width" id="materiel_unit" style="width: 120px">
                                                <option value="">请选择物料单位</option>
                                                <option v-for="unit in pagePara.unitInfo" v-bind:value="unit.id">{{unit.text}}</option>
                                            </select>
                                        </div>
                                        <div class="form-group">
                                            <input class="form-control" id="order_id" placeholder="销售订单号" style="width: 120px;">
                                        </div>
                                        <div class="form-group">
                                            <button id="search" class="btn btn-info" v-on:click="search">
                                                <i class="fa fa-search"></i>
                                            </button>
                                        </div>
                                        <div class="form-group">
                                            <button id="scan" class="btn btn-info" v-on:click="syncSafeMaterial">
                                                同步安全库存物料
                                            </button>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="btn-group col-sm-10" style="margin-bottom: 10px;padding: 0px;">
                            <button type="button" class="btn btn-white" v-for="model in pagePara.stockModelInfo" v-bind:class="{active: selectModelType == model.id}" @click="switchModel(model.id)">{{model.text}}</button>
                            <div>
                                <span style="margin-left: 5px">
                                    <button id="synchronize" class="btn btn-info" v-on:click="scanSafePurchase">
                                    安全库存扫描
                                    </button>
                                </span>
                                <span>
                                    <button id="idleScan" class="btn btn-info" v-on:click="scanIdle">
                                    闲置库存扫描
                                    </button>
                                </span>
                            </div>
                        </div>
                        <table id="inventoryTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCAInventoryControl?method=';

  import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'

  export default {
    data: function() {
      return {
        pagePara: {},
        apiName: common.getApiName(apiUrl),
        workRow: {},
        oldRow: '',
        selectModelType: 1
      }
    },
    name: 'NCAInventoryControl',
    components: {
      DomainChooseHeader
    },
    mounted: function() {
      let _self = this;
      let $table = $('#inventoryTable');

      function queryParams(params) {
        let domain_id = common.getSelect2Val('domain_select')
        if (domain_id) {
          params.domain_id = domain_id
        }
        if($('#search_text').val()){
          params.search_text = $('#search_text').val()
        }
        if($('#warehouse_id').val()){
          params.warehouse_id = $('#warehouse_id').val()
        }
        if($('#is_idle_stock').val()){
          params.is_idle_stock = $('#is_idle_stock').val()
        }
        if($('#order_id').val()){
          params.order_id = $('#order_id').val()
        }
        if($('#materiel_unit').val()){
          params.materiel_unit = $('#materiel_unit').val()
        }
        if($('#warehouse_zone_id').val()){
          params.warehouse_zone_id = $('#warehouse_zone_id').val()
        }
        params.stock_model = _self.selectModelType;
        return JSON.stringify(params);
      }

      window.tableEvents = {
        'click .show_detail': function(e, value, row, index) {
          _self.$router.push({
            path: '/nca/inventorymanage/NCAInventoryZoneDetailControl',
            query: {
              stockmap_id: row.stockmap_id
            }
          })
        },
        'click .edit_detail': function(e, value, row, index) {
          _self.$router.push({path: '/nca/inventorymanage/NCABuyInMovingMaterielControl',
              query: row
          });
        }
      };

      function clickFormatter(value, row) {
        if (row.materiel_manage === '1'){
          return [
            '<a class="show_detail" style="cursor: pointer;">',
            value,
            '</a>'
          ].join('')
        } else {
          return [
            value
          ].join('')
        }
      }

      function editFormatter(value, row) {
        return [
            '<a class="btn btn-info btn-xs m-r-5 edit_detail">移库</a>'
        ].join('')
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
            common.BTRowFormatWithFE('materiel_code', '物料编码', clickFormatter,tableEvents),
            common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
            common.BTRowFormat('materiel_format', '规格型号'),
            common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
            common.BTRowFormatAlignRight('current_amount', '数量'),
            common.BTRowFormatAlignLeft('warehouse_name', '仓库名称'),
            common.BTRowFormatAlignLeft('zone_name', '仓区名称'),
            common.BTRowFormat('order_id', '销售订单号'),
            common.BTRowFormatEditableRight('safe_amount', '安全库存'),
            common.BTRowFormatEditableRight('min_purchase_amount', '最低采购数量'),
            common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '库存管理模式', 'stockModelInfo'),
            common.BTRowFormatEdSelect2Disabled(_self, 'is_idle_stock', '是否闲置库存', 'tfInfo'),
            common.BTRowFormat('create_date', '创建日期'),
            common.BTRowFormatEdSelect2Disabled(_self, 'trigger_safe_model', '状态', 'safeModelStateInfo'),
            common.actFormatter('edit', editFormatter, tableEvents)
          ],
          idField: 'stockmap_id',
          uniqueId: 'stockmap_id',
          striped: true,
//          showExport: true,
          clickToSelect: true,
          pagination: true,
          pageSize:common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: function(field, row, $el, editable) {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: function(field, row, oldValue, $el) {
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockmap_id', $table)
          }
        });
        common.changeTableClass($table)
        $('#inventoryTable').bootstrapTable('hideColumn', 'safe_amount');
        $('#inventoryTable').bootstrapTable('hideColumn', 'min_purchase_amount');
        $('#inventoryTable').bootstrapTable('hideColumn', 'trigger_safe_model');
      }

      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
          _self.selectModelType = _self.pagePara.stockModelInfo[0].id
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function initPage() {
        await getInit()
        $('#domain_select').on('change', function(evt) {
          _self.search()
        });
        initTable()
      }

      $(async function() {
        await initPage();
      });
    },
    methods: {
      getWarehouseZone: async function() {
        let _self = this;
        let warehouseId = $('#warehouse_id').val();
        if (warehouseId) {
          let response = await _self.$http.post(apiUrl + 'getWarehouseZone', {
            warehouse_id: warehouseId
          });
          _self.$set(_self.pagePara, 'warehouseZoneInfo', response.data.info);
        } else {
          _self.$set(_self.pagePara, 'warehouseZoneInfo', []);
        }
      },
      search: function(event) {
        $('#inventoryTable').bootstrapTable("refresh")
      },
      switchModel: function (index) {
        let _self = this
        _self.selectModelType = index
        if(index == 2) {//销售订单管理
          $('#inventoryTable').bootstrapTable('hideColumn', 'safe_amount');
          $('#inventoryTable').bootstrapTable('showColumn', 'order_id');
          $('#inventoryTable').bootstrapTable('showColumn', 'is_idle_stock');
          $('#inventoryTable').bootstrapTable('hideColumn', 'min_purchase_amount');
          $('#inventoryTable').bootstrapTable('showColumn', 'warehouse_name');
          $('#inventoryTable').bootstrapTable('showColumn', 'zone_name');
          $('#inventoryTable').bootstrapTable('hideColumn', 'trigger_safe_model');
          $('#inventoryTable').bootstrapTable('showColumn', 'edit');
        } else if (index == 1) {//安全库存管理
          $('#inventoryTable').bootstrapTable('showColumn', 'safe_amount');
          $('#inventoryTable').bootstrapTable('hideColumn', 'order_id');
          $('#inventoryTable').bootstrapTable('hideColumn', 'is_idle_stock');
          $('#inventoryTable').bootstrapTable('showColumn', 'min_purchase_amount');
          $('#inventoryTable').bootstrapTable('hideColumn', 'warehouse_name');
          $('#inventoryTable').bootstrapTable('hideColumn', 'zone_name');
          $('#inventoryTable').bootstrapTable('showColumn', 'trigger_safe_model');
          $('#inventoryTable').bootstrapTable('hideColumn', 'edit');
        }
        $('#inventoryTable').bootstrapTable("refresh")
      },
      syncSafeMaterial: async function () {
        let _self = this
        let param = {}
        let response = await _self.$http.post(apiUrl + 'sync_safe_material', param)
        common.dealPromptCommon('安全库存物料已同步')
        $('#inventoryTable').bootstrapTable("refresh")
      },
      scanSafePurchase: async function () {
        let _self = this
        let param = {}
        let response = await _self.$http.post(apiUrl + 'scan_safe_purchase', param)
        common.dealPromptCommon('安全库存扫描完成')
        $('#inventoryTable').bootstrapTable("refresh")
      },
      scanIdle: async function() {
        let _self = this
        let param = {}
        let response = await _self.$http.post(apiUrl + 'scan_idle', param);
        common.dealPromptCommon('闲置库存扫描完成')
      }
    }
  }
</script>
<style scoped>
</style>
