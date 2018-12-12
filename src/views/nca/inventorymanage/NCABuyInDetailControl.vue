<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">入库管理</a></li>
            <li class="active">入库任务单</li>
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
                        <div class="row" style="margin-top: 10px">
                            <div class="form-group col-sm-3">
                                <label class="col-sm-6 control-label">质量检验单号</label>
                                <div class="col-sm-6">
                                    <input class="form-control" v-model="queryData.qualitycheck_id" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                        </div>

                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索物料编码、物料名称" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="searchOther"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addOtherM">物料入库</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="qualityTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="addZoneModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">物料入库存储信息</h4>
                    </div>
                    <form @submit.prevent="addZoneAct" id="zoneForm">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">请选择仓库</label>
                                    <div class="col-sm-6">
                                        <select class="select2 form-control select-width" id="warehouse_id" style="width: 100px" v-on:change="getWarehouseZone" data-parsley-required="true">
                                            <option value="">选择仓库</option>
                                            <option v-for="warehouse in pagePara.housesInfo" v-bind:value="warehouse.id">{{warehouse.text}}</option>
                                        </select>                                    </div>
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
                queryData: {},
                searchText: null,
                qualitySelects: []
            }
        },
        name: 'NCABuyInDetailControl',
        methods: {
            queryParams: function (params) {
                if($('#warehouse_id').val()){
                    params.warehouse_id = $('#warehouse_id').val()
                }
                if($('#warehouse_zone_id').val()){
                    params.warehouse_zone_id = $('#warehouse_zone_id').val()
                }
                params.qualitycheck_id = this.queryData.qualitycheck_id;
                params.search_keyword = this.searchText;
                return JSON.stringify(params);
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
            searchOther: async function() {
                $('#qualityTable').bootstrapTable("refresh");
            },
            initTable: function () {
                let _self = this;
                window.tableEvents = {
                    'click .purchaseSelect': function(e, value, row, index) {
                        if ($(this).prop('checked')) {
                            _self.qualitySelects.push(row)
                        } else {
                            _self.qualitySelects.splice($.inArray(row, _self.qualitySelects), 1);
                        }
                    }
                };
                this.masterTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getQualityCheckOrderDetail',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    columns: [
                        common.actFormatter('checkAct', () => {return `<input type="checkbox" class="purchaseSelect">`}, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格'),
                        common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'materielInfo'),
                        common.BTRowFormatAlignRight('qualified_number', '检验单数量'),
                        common.BTRowFormatAlignRight('finishStock_number', '已入库数量'),
                        common.BTRowFormat('order_id', '销售单号')
                    ],
                    sortOrder:'asc',
                    idField: 'qualitycheckdetail_id',
                    uniqueId: 'qualitycheckdetail_id',
                    toolbar: '#toolbar',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageNumber: 1,
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {

                    },
                    onEditableSave: (field, row, oldValue, $el) => {

                    }
                });
                common.changeTableClass(this.masterTable)
            },
            initPage: function () {
                this.$http.post(apiUrl + 'init', { }).then(response => {
                    this.pagePara = response.data.info;
                    this.initTable();
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });
            },
            addOtherM: function() {
                let _self = this;
                if (_self.qualitySelects.length <= 0) {
                    return common.dealPromptCommon('请选择物料')
                }
                $('#addZoneModal').modal('show')
            },
            addZoneAct: function (event) {
                let _self = this
                if ($('#warehouse_id').val() == '' ) {
                    return common.dealPromptCommon('请选择仓库')
                } else if ($('#warehouse_zone_id').val() == '' ) {
                    return common.dealPromptCommon('请选择仓区')
                } else {
                    _self.$router.push({path: 'NCABuyInMaterielControl', query: {
                        warehouse_id: $('#warehouse_id').val(),
                        warehouse_zone_id: $('#warehouse_zone_id').val(),
                        qualitycheck_id: _self.queryData.qualitycheck_id,
                        materiels:_self.qualitySelects,
                    }});
                    $('#addZoneModal').modal('hide')
                }
            }
        },
        mounted: function() {
            this.queryData = this.$route.query;
            this.masterTable = $('#qualityTable');
            this.initPage();
        }
    }
</script>
<style scoped>
    .select-width {
        width: 100px;
    }
</style>
