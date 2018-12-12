<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">移库</a></li>
            <li class="active">物料移库单</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <form @submit.prevent="addAct2" id="movingForm">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <button type="submit" class="btn btn-info btn-xs" @click="addAct">提交</button>
                                <a class="btn btn-info btn-xs"  href="javascript:history.back(-1)">返回</a>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand">
                                    <i class="fa fa-expand"></i>
                                </a>
                            </div>
                            <h4 class="panel-title">物料移库单</h4>
                        </div>
                        <div class="panel-body">
                            <div class="row" >
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">仓库名称</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.warehouse_name" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">仓区名称</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.zone_name" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">物料编码</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.materiel_code" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">物料名称</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.materiel_name" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">规格</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.materiel_format" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">单位</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" v-model="queryData.materiel_unit" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">移库数量</label>
                                    <div class="col-sm-6">
                                        <input class="form-control" id="current_amount" v-show="typeof(queryData.current_amount) != 'undefined'" v-model="queryData.current_amount">
                                        <input class="form-control" id="item_amount" v-show="typeof(queryData.item_amount) != 'undefined'" v-model="queryData.item_amount">
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">目标仓库</label>
                                    <div class="col-sm-6">
                                        <select class="select2 form-control select-width" id="warehouse_id" style="width: 100px" v-on:change="getWarehouseZone" data-parsley-required="true">
                                            <option value="">选择仓库</option>
                                            <option v-for="warehouse in pagePara.warehousesInfo" v-bind:value="warehouse.id">{{warehouse.text}}</option>
                                        </select>                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-6 control-label">目标仓区</label>
                                    <div class="col-sm-6">
                                        <select class="select2 form-control select-width" id="warehouse_zone_id" style="width: 100px">
                                            <option value="">选择仓区</option>
                                            <option v-for="zone in pagePara.warehouseZoneInfo" v-bind:value="zone.id">{{zone.text}}</option>
                                        </select>
                                    </div>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
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
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">库存信息</h4>
                    </div>
                    <div class="panel-body">
                        <table id="movingMaterielTable"></table>
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
                oldRow: '',
                queryData: {},
                columsArr: []
            }
        },
        name: 'NCABuyInMovingMaterielControl',
        mounted: function() {
            let _self = this;
            let $table = $('#movingMaterielTable');
            _self.queryData = _self.$route.query
            _self.stockmap_id = _self.$route.query.stockmap_id
            _self.materiel_manage = _self.$route.query.materiel_manage
            _self.current_amount = _self.$route.query.current_amount
            _self.item_amount = _self.$route.query.item_amount
            _self.materiel_id = _self.$route.query.materiel_id

            function queryParams(params) {
                let domain_id = common.getStoreData('userinfo').domain_id
                if (domain_id) {
                    params.domain_id = domain_id
                }
                if($('#warehouse_id').val()){
                    params.warehouse_id = $('#warehouse_id').val()
                }
                if($('#warehouse_zone_id').val()){
                    params.warehouse_zone_id = $('#warehouse_zone_id').val()
                }
                params.stockitem_id =  _self.$route.query.stockitem_id
                params.stockmap_id =  _self.$route.query.stockmap_id
                params.materiel_manage = _self.$route.query.materiel_manage
                params.materiel_id = _self.$route.query.materiel_id
                params.current_amount = $('#current_amount').val()
                params.item_amount = $('#item_amount').val()
                return JSON.stringify(params);
            }

            function initTable() {
                console.log('_self.materiel_manage',_self.materiel_manage)
                if (_self.materiel_manage == 2) {
                    _self.columsArr = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('current_amount', '数量')
                    ];
                } else {
                    _self.columsArr = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('item_amount', '数量')
                    ];
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_materiel',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: _self.columsArr,
                    sortOrder:'asc',
                    idField: 'stockmap_id',
                    uniqueId: 'stockmap_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockmap_id', $table)
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
            search: async function(event) {
                let _self = this;
                $('#movingMaterielTable').bootstrapTable("refresh")
            },
            addAct2: function(event) {},
            addAct:function(event){
                let _self = this;
                if ($('#movingForm').parsley().isValid()) {
                    try{
                        var params = {
                            stockmap_id: _self.$route.query.stockmap_id,
                            stockitem_id:  _self.$route.query.stockitem_id,
                            materiel_id:  _self.$route.query.materiel_id,
                            warehouse_id: $('#warehouse_id').val(),
                            warehouse_zone_id: $('#warehouse_zone_id').val(),
                            current_amount: $('#current_amount').val(),
                            item_amount: $('#item_amount').val()
                        }
                        _self.$http.post(apiUrl + 'modify_moving', params).then((response) => {
                            _self.workRow = {};
                            console.log('add success');
                            window.history.back()
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            }
        }
    }
</script>
<style scoped>
.select-width {
    width: 100px;
}
</style>
