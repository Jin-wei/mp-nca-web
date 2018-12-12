<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;" @click="validateZone">仓库仓区管理</a></li>
            <li class="active">仓库详情</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <form @submit.prevent="addAct" id="detailForm">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <button type="submit" v-if="warehouse_id" class="btn btn-info btn-xs">保存</button>
                                <button type="submit" v-else="" class="btn btn-info btn-xs">确认</button>
                                <a class="btn btn-info btn-xs" href="javascript:;" @click="validateZone">返回</a>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                            </div>
                            <h4 class="panel-title">仓库详情</h4>
                        </div>
                        <div class="panel-body">
                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>仓库名称</span>
                                        <input class="form-control" placeholder="请输入仓库名称" v-model="warehouse.warehouse_name"
                                               data-parsley-required="true" data-parsley-maxlength="60" v-bind:disabled="warehouse_id">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>仓库编码</span>
                                        <input class="form-control" placeholder="请输入仓库编码" v-model="warehouse.warehouse_code"
                                               data-parsley-required="true" data-parsley-maxlength="20" v-bind:disabled="warehouse_id">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>仓库类型</span>
                                        <select class="form-control" v-model="warehouse.warehouse_type" data-parsley-required="true">
                                            <option value="">请选择仓库类型</option>
                                            <option v-for="type in pagePara.warehouseTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-8">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>详细地址</span>
                                        <input class="form-control" placeholder="请输入详细地址" v-model="warehouse.warehouse_address"
                                               data-parsley-required="true" data-parsley-maxlength="100">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>状态</span>
                                        <select class="form-control" id="state" v-model="warehouse.warehouse_state" data-parsley-required="true">
                                            <option value="">请选择仓库状态</option>
                                            <option v-for="state in pagePara.warehouseStateInfo" v-bind:value="state.id">{{state.text}}</option>
                                        </select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>联系人</span>
                                        <input class="form-control" placeholder="请输入联系人" v-model="warehouse.warehouse_contact"
                                               data-parsley-required="true" data-parsley-maxlength="20">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>联系人电话</span>
                                        <input class="form-control" placeholder="请输入联系人电话" v-model="warehouse.warehouse_phone"
                                               data-parsley-required="true" data-parsley-phone="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon">联系人传真</span>
                                        <input class="form-control" placeholder="请输入联系人传真" v-model="warehouse.warehouse_fax"
                                               data-parsley-required="false" data-parsley-maxlength="20">
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </form>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button class="btn btn-info btn-xs" @click="addZone">增加</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">仓区详情</h4>
                    </div>
                    <div class="panel-body">
                        <table id="warehouseZoneTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="addZoneModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">增加仓区</h4>
                    </div>
                    <form @submit.prevent="addZoneAct" id="zoneForm">
                        <div class="modal-body">
                            <div class="form-horizontal">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>仓区名称</label>
                                    <div class="col-md-9">
                                        <input class="form-control" v-model="workRow.zone_name" maxlength="25" data-parsley-maxlength="25" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group">
                                    <label class="col-md-2 control-label">备注</label>
                                    <div class="col-md-9">
                                        <input class="form-control" v-model="workRow.zone_remark" maxlength="100" data-parsley-maxlength="100">
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
  const common = require('commonFunc');
  const apiUrl = '/api/nca/inventorymanage/NCAWarehouseControl?method=';

  export default {
    data: function() {
      return {
        pagePara: {},
        workRow: {},
        warehouse_id: null,
        warehouse: {},
        oldRow: '',
        warehouseZones: []
      }
    },
    name: 'NCAWarehouseDetailControl',
    mounted: function() {
      let _self = this;
      let $table = $('#warehouseZoneTable');
      _self.warehouse_id = _self.$route.query.warehouse_id
      async function getInit() {
        try {
          let response = await _self.$http.post(apiUrl + 'init', {});
          _self.pagePara = response.data.info
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      async function getWarehouse() {
        try {
          let param = {
            warehouse_id: _self.warehouse_id
          }
          let response = await _self.$http.post(apiUrl + 'search_warehouse', param);
          _self.warehouse = response.data.info
        } catch (error) {
          common.dealErrorCommon(_self, error)
        }
      }

      function queryParams(params) {
        if (_self.warehouse_id){
          params.warehouse_id = _self.warehouse_id
        }
        return JSON.stringify(params);
      }

      function operateFormatter(value, row) {
          return [
             '<a class="btn btn-xs btn-info m-r-5 tableDelete">删除</a>'
          ].join('')
      }

      function initTable() {
        window.tableEvents = {
          'click .tableDelete': function(e, value, row, index) {
            common.rowDeleteWithApi(_self, '仓区删除', apiUrl + 'delete_zone', $table, row, 'warehouse_zone_id', function(){})
          }
        };
        $table.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'search_zone',
          queryParams: queryParams,
          sidePagination: 'server',
          ajaxOptions: common.bootstrapTableAjaxOtions,
          responseHandler: function(res) {
            _self.warehouseZones = res.info.rows
            return res.info;
          },
          height: common.getTableHeight(),
          columns: [
            common.BTRowFormatWithIndex('NO.'),
            common.BTRowFormatAlignLeft('zone_name', '仓区名称'),
            common.BTRowFormatEdTextareaWidthAlignLeft('zone_remark', '备注',120,7),
            common.actFormatter('act', operateFormatter, tableEvents)
          ],
          idField: 'warehouse_zone_id',
          uniqueId: 'warehouse_zone_id',
          striped: true,
          clickToSelect: true,
          pagination: false,
          pageSize:common.pageSize(),
          pageList: [10, 15, 25, 50, 100],
          locale: 'zh-CN',
          onEditableShown: function(field, row, $el, editable) {
            _self.oldRow = $.extend(true, {}, row)
          },
          onEditableSave: function(field, row, oldValue, $el) {
            common.rowModifyWithT(_self, apiUrl + 'modify_zone', row, 'warehouse_zone_id', $table)
          }
        });
        common.changeTableClass($table)
      }

      async function initPage() {
        await getInit()
        if (_self.warehouse_id){
          await getWarehouse()
        }
        initTable()
        $('#detailForm').parsley();
        $('#zoneForm').parsley();
      }

      $(async function() {
        await initPage()
      });
    },
    methods: {
      addAct: function(event) {
        let _self = this;
        if ($('#detailForm').parsley().isValid()) {
          let apiName = _self.warehouse_id ? 'modify' : 'add'
          if(_self.warehouse_id){
            _self.warehouse.warehouse_id = _self.warehouse_id
          }
          let modifyData = {
            'old': _self.warehouse,
            'new': _self.warehouse
          }
          let param = _self.warehouse_id ? modifyData : _self.warehouse
          _self.$http.post(apiUrl + apiName, param).then((response) => {
            let msg = _self.warehouse_id ? '保存成功' : '创建成功'
            common.dealSuccessCommon(msg)
            let retData = response.data.info;
            if (retData) {
              _self.warehouse_id = retData.warehouse_id
            }
            $('#detailForm').parsley().reset();
          }, (response) => {
            common.dealErrorCommon(_self, response)
          });
        }
      },
      addZone: function () {
        let _self = this;
        _self.workRow = {}
        if (_self.warehouse_id) {
          $('#addZoneModal').modal('show')
        } else {
          common.dealPromptCommon('请先保存仓库信息')
        }
      },
      addZoneAct: function (event) {
        let _self = this;
        if ($('#zoneForm').parsley().isValid()){
          _self.workRow.warehouse_id = _self.warehouse_id
          _self.$http.post(apiUrl + 'add_zone', _self.workRow).then((response) => {
            let retData = response.data.info;
            $('#addZoneModal').modal('hide')
            $('#warehouseZoneTable').bootstrapTable("refresh", _self.queryParams);
          }, (response) => {
            common.dealErrorCommon(_self, response)
          });
        }
      },
      validateZone: function () {
        let _self = this
        console.log(1111)
        if (_self.warehouseZones.length > 0){
            window.history.back()
        } else {
          common.dealPromptCommon('请增加仓区')
        }
      }
    }
  }
</script>
<style scoped>
</style>
