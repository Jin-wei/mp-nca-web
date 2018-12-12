<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">实时库存</a></li>
            <li class="active">仓储详情</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">仓储详情</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
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
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="warehouseZoneTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCAInventoryControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: ''
      }
    },
    name: 'NCAInventoryZoneDetailControl',
    mounted: function() {
      let _self = this;
      let $table = $('#warehouseZoneTable');

      function queryParams(params) {
        if($('#warehouse_id').val()){
          params.warehouse_id = $('#warehouse_id').val()
        }
        if($('#warehouse_zone_id').val()){
          params.warehouse_zone_id = $('#warehouse_zone_id').val()
        }
        params.stockmap_id =  _self.$route.query.stockmap_id
        return JSON.stringify(params);
      }

      window.tableEvents = {
        'click .edit_detail': function(e, value, row, index) {
            _self.$router.push({path: '/nca/inventorymanage/NCABuyInMovingMaterielControl',
                query: row
            });
        }
      };

      function editFormatter(value, row) {
        return [
            '<a class="btn btn-info btn-xs m-r-5 edit_detail">移库</a>'
        ].join('')
      }

      function initTable() {
        $table.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_stockItem',
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
            common.BTRowFormatAlignRight('item_amount', '数量'),
            common.BTRowFormatAlignLeft('warehouse_name', '仓库名称'),
            common.BTRowFormatAlignLeft('zone_name', '仓区名称'),
            common.actFormatter('edit', editFormatter, tableEvents)
          ],
          sortOrder:'asc',
          idField: 'stockitem_id',
          uniqueId: 'stockitem_id',
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
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockitem_id', $table)
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

      $(function() {
        initPage();
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
        $('#warehouseZoneTable').bootstrapTable("refresh")
      }
    }
  }
</script>
<style scoped>
</style>
