<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">库存管理</a></li>
            <li class="active">仓库仓区列表</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">仓库仓区列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_warehouse" placeholder="搜索仓库编号、仓库名称" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="warehouseTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCAWarehouseControl?method=';

  function queryParams(params) {
    params.search_warehouse = $('#search_warehouse').val()
    return JSON.stringify(params);
  }
  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        oldRow: ''
      }
    },
    name: 'NCAWarehouseControl',
    mounted: function() {
      let _self = this;
      let $table = $('#warehouseTable');
      let rowDelete = function(_self, msg, apiUrl, row, key) {
        common.dealConfrimCommon(msg, function() {
          _self.$http.post(apiUrl + 'delete', row).then((response) => {
            $('#warehouseTable').bootstrapTable('remove', {
              field: key,
              values: [row[key]]
            });
            common.dealSuccessCommon('删除成功');
          }, (response) => {
            common.dealErrorCommon(_self, response);
          });
        });
      };

      function operateFormatter(value, row, index) {
        return [
          '<a class="btn btn-info btn-xs m-r-5 detail">查看</a>',
//          '<a class="btn btn-primary btn-xs m-r-5 delete">删除</a>',
        ].join('')
      }

      function initTable() {
        window.tableEvents = {
          'click .delete': function(e, value, row, index) {
            rowDelete(_self, '删除', apiUrl, row, 'warehouse_id');
          },
          'click .detail': function(e, value, row, index) {
            _self.$router.push({
              path: '/nca/inventorymanage/NCAWarehouseDetailControl',
              query: {
                warehouse_id: row.warehouse_id
              }
            })
          }
        };
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
            common.BTRowFormat('warehouse_code', '仓库编号'),
            common.BTRowFormatAlignLeft('warehouse_name', '仓库名称'),
            common.BTRowFormatEdSelect2(_self, 'warehouse_type', '仓库类型', 'warehouseTypeInfo'),
            common.BTRowFormatEditableLeft('warehouse_contact', '联系人'),
            common.BTRowFormatEditable('warehouse_phone', '联系人电话'),
            common.BTRowFormatEdTextareaWidthAlignLeft('warehouse_address', '地址',120,7),
            common.actFormatter('act', operateFormatter, tableEvents)
          ],
          sortOrder:'asc',
          idField: 'warehouse_id',
          uniqueId: 'warehouse_id',
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
            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'warehouse_id', $table)
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
      addM: function(event) {
        let _self = this
        _self.$router.push({
          path: '/nca/inventorymanage/NCAWarehouseDetailControl',
          query: {}
        })
      },
      search: function(event) {
        $('#warehouseTable').bootstrapTable("refresh", queryParams)
      }
    }
  }
</script>
<style scoped>
</style>
