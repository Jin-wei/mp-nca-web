<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">入库管理</a></li>
            <li class="active">入库任务</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">入库任务单</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索物料编码、物料名称" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="stockIn" v-bind:disabled="stockInState === '3'">物料入库</button>
                            </div>
                        </div>
                    </div>

                    <div class="panel-body auto-height">
                        <table id="productInItemsTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="zoneModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">物料入库存储信息</h4>
                    </div>
                    <form @submit.prevent="goToSubmit" id="zoneForm">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">请选择仓库</label>
                                    <div class="col-sm-6">
                                        <select class="select2 form-control select-width" id="warehouse_id" style="width: 100px" v-on:change="getWarehouseZone" data-parsley-required="true">
                                            <option value="">选择仓库</option>
                                            <option v-for="warehouse in pagePara.housesInfo" v-bind:value="warehouse.id">{{warehouse.text}}</option>
                                        </select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">请选择仓区</label>
                                    <div class="col-sm-6">
                                        <select class="select2 form-control select-width" id="warehouse_zone_id" style="width: 100px">
                                            <option value="">选择仓区</option>
                                            <option v-for="zone in pagePara.warehouseZoneInfo" v-bind:value="zone.id">{{zone.text}}</option>
                                        </select>
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
    const apiUrl = '/api/nca/inventorymanage/NCABuyInControl?method=';

    export default {
        data: function() {
            return {
                masterTable: null,
                pagePara: {},
                searchText: null,
                itemSelects: [],
                stockInState: ''
            }
        },
        name: 'NCAProductInItemsControl',
        methods: {
            search: async function() {
              this.getProductItems()
            },
            initTable: function () {
                let _self = this

              function typeFormatter(value,row) {
                if (row.taskrelated_type) {
                  return row.taskrelateddesign_number
                } else {
                  return row.taskdesign_number
                }
              }

              function stockTypeFormatter(value,row) {
                if (row.taskrelated_type) {
                  return row.related_stock_in_number
                } else {
                  return row.stock_in_number
                }
              }
                _self.masterTable.bootstrapTable({
                    columns: [
                        {field: '', checkbox: true},
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格'),
                        common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'materielInfo'),
                        common.BTRowFormatWithFormatterAlignRight('', '需求数量',typeFormatter),
                        common.BTRowFormatWithFormatterAlignRight('', '已入库数量',stockTypeFormatter)
                    ],
                    sortOrder:'asc',
                    idField: 'productivetaskrelated_id',
                    uniqueId: 'productivetaskrelated_id',
                    toolbar: '#toolbar',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    locale: 'zh-CN',
                  onCheckAll: function(rows) {
                    for (let i = 0; i < rows.length; i++) {
                      _self.itemSelects.push(rows[i])
                    }
                  },
                  onUncheckAll: function(rows) {
                    _self.itemSelects = []
                  },
                  onCheck: function(row, $element) {
                    _self.itemSelects.push(row);
                  },
                  onUncheck: function(row, $element) {
                    _self.itemSelects.splice($.inArray(row, _self.itemSelects), 1);
                  }
                });
                common.changeTableClass(this.masterTable)
            },
            initPage: function () {
              this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
                this.getProductItems();
                this.initTable();
              }, (error) => {
                common.dealErrorCommon(this, error)
              });
            },
          getProductItems: function () {
            let params = {
              search_text: this.searchText || '',
              productivetask_id: this.productivetask_id
            }
            this.$http.post(apiUrl + 'getProductInItems', params).then(response => {
              let retData = response.data.info;
              if (retData.length > 0) {
                this.stockInState = retData[0].stock_in_state
              }
              this.masterTable.bootstrapTable('load', {
                data: retData
              })
            }, (error) => {
              common.dealErrorCommon(this, error)
            });
          },
            refreshMateriel: function() {
                this.masterTable.bootstrapTable("refresh");
            },
            stockIn: function () {
              if (this.itemSelects.length <= 0) {
                return common.dealPromptCommon('请选择物料')
              }
              $('#zoneModal').modal('show')
            },
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
            goToSubmit: function () {
              let _self = this
              if (!$('#warehouse_id').val()) {
                return common.dealPromptCommon('请选择仓库')
              } else if (!$('#warehouse_zone_id').val()) {
                return common.dealPromptCommon('请选择仓区')
              } else {
                this.$router.push({path: 'NCAProductInSubmitControl', query: {
                  warehouse_id: $('#warehouse_id').val(),
                  warehouse_zone_id: $('#warehouse_zone_id').val(),
                  productivetask_id: _self.productivetask_id,
                  itemSelects:_self.itemSelects
                }});
                $('#zoneModal').modal('hide')
              }
            }
        },
        mounted: function() {
            let queryData = this.$route.query;
            this.productivetask_id = queryData.productivetask_id;
            this.masterTable = $('#productInItemsTable');
            this.initPage();
        }
    }
</script>
<style scoped>
</style>
