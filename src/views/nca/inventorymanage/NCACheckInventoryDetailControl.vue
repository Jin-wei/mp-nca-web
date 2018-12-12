<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">仓库管理</a></li>
            <li class="active">盘点单</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <form @submit.prevent="addAct2" id="detailForm">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <span v-show="workRow.checkInventory_state != 3">
                                    <button v-if="workRow.checkInventory_state == 2" class="btn btn-info btn-xs" @click="reviewAct">审核</button>
                                    <button type="submit" v-else="" class="btn btn-info btn-xs" @click="addAct">确认</button>
                                </span>
                                <a class="btn btn-info btn-xs" href="javascript:history.back(-1)">返回</a>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                                   data-click="panel-expand"><i class="fa fa-expand"></i></a>
                            </div>
                            <h4 class="panel-title">盘点单</h4>
                        </div>
                        <div class="panel-body">
                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"  id="checkinventory_id" >盘点单号</span>
                                        <input class="form-control" v-model="workRow.checkinventory_id"
                                               v-bind:disabled="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>仓库名称</span>
                                        <select class="select2 form-control select-width" id="warehouse_id"
                                                v-on:change="getWarehouseZone" v-model="workRow.warehouse_id"
                                                v-bind:disabled="checkInventoryId.length>0" data-parsley-required="true">
                                            <option value="">请选择仓库</option>
                                            <option v-for="warehouse in pagePara.warehousesInfo"
                                                    v-bind:value="warehouse.id">{{warehouse.text}}</option>
                                        </select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>仓区名称</span>
                                        <div class="form-group">
                                            <select class="select2 form-control select-width" id="warehouse_zone_id"
                                                    v-model="workRow.warehouse_zone_id"
                                                    v-bind:disabled="checkInventoryId.length>0" data-parsley-required="true">
                                                <option value="">请选择仓区</option>
                                                <option v-for="zone in pagePara.warehouseZoneInfo"
                                                        v-bind:value="zone.id">{{zone.text}}</option>
                                            </select>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-4">
                                    <div class="input-group ">
                                        <span class="input-group-addon"><span class="table-required">*</span>计划盘点时间</span>
                                            <input type="text" class="form-control" id="check_plan_date"
                                                   v-bind:disabled="checkInventoryId.length>0" data-parsley-required="true"/>
                                    </div>
                                </div>
                                <div class="form-group col-sm-4">
                                    <div class="input-group">
                                        <span class="input-group-addon"><span class="table-required">*</span>盘点人</span>
                                        <input class="form-control" placeholder="请输入盘点人" id="check_checker"
                                               v-model="workRow.check_checker" v-bind:disabled="checkInventoryId.length>0"
                                               data-parsley-required="true">
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
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">盘点的物料</h4>
                    </div>
                    <div class="panel-body">
                        <table id="Table"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCACheckInventoryControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                workRow: {},
                warehouse: {},
                oldRow: '',
                checkInventoryId: ''
            }
        },
        name: 'NCACheckInventoryDetailControl',
        mounted: function() {
            let _self = this;
            let $table = $('#Table');
            _self.checkInventoryId = _self.$route.query.checkInventoryId

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            async function getInventoryDetail() {
                try {
                    let param = {
                        checkinventory_id: _self.checkInventoryId
                    }
                    let response = await _self.$http.post(apiUrl + 'search', param);
                    let warehouse_id = response.data.info.rows[0].warehouse_id
                    let zones = await _self.$http.post(apiUrl + 'getWarehouseZone', {
                        warehouse_id: warehouse_id
                    });
                    _self.$set(_self.pagePara, 'warehouseZoneInfo', zones.data.info);
                    _self.workRow = response.data.info.rows[0]
                    $('#check_plan_date').val(_self.workRow.check_plan_date)

                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            function queryParams(params) {
                params.checkinventory_id =  _self.checkInventoryId
                if($('#warehouse_id').val()){
                    params.warehouse_id = $('#warehouse_id').val()
                }
                if($('#warehouse_zone_id').val()){
                    params.warehouse_zone_id = $('#warehouse_zone_id').val()
                }
                params.check_plan_date = $('#check_plan_date').val();
                return JSON.stringify(params);
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_d',
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
                        common.BTRowFormatAlignRight('check_stock_amount', '库存数量'),
                        common.BTRowFormatAlignRight('check_actual_amount', '盘点结果'),
                        common.BTRowFormatAlignRight('check_profit', '库存损益')
                    ],
                    idField: 'checkitem_id',
                    uniqueId: 'checkitem_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'checkitem_id', $table)
                    }
                });
                common.changeTableClass($table)
            }

            async function initPage() {
                await getInit()
                if(_self.checkInventoryId){
                    await getInventoryDetail()
                }
                if (_self.warehouse_id){
                    await getWarehouse()
                }
                initTable()
                $('#detailForm').parsley();
            }

            $(async function() {
                await initPage()
                common.initDatepicker($('#check_plan_date'));
            });
        },
        methods: {
            addAct2: function(event) {},
            addAct: function(event) {
                let _self = this;
                if ($('#detailForm').parsley().isValid()) {
                    try{
                        var params = {
                            warehouse_id: $('#warehouse_id').val(),
                            warehouse_zone_id: $('#warehouse_zone_id').val(),
                            check_plan_date: $('#check_plan_date').val(),
                            check_checker: $('#check_checker').val()
                        }

                        _self.$http.post(apiUrl + 'add', params).then((response) => {
                            $('#detailForm').bootstrapTable("refresh", _self.queryParams)
                            _self.workRow = {};
                            console.log('add success');
                            $('#detailForm').parsley().reset();
                            window.history.back()
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
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
            reviewAct: async function () {
                let _self = this
                let param = {
                    checkinventory_id: _self.workRow.checkinventory_id
                }
                let response = await _self.$http.post(apiUrl + 'check_review', param);
                window.history.back()
                $('#detailForm').bootstrapTable("refresh", _self.queryParams)
                console.log('reviewAct'+response)
            }
        }
    }
</script>
<style scoped>
</style>
