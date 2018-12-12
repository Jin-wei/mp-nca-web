<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">其他出库申请</a></li>
            <li class="active">出库申请单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 col-sm-12">
                <div class="widget widget-stats bg-black-lighter">
                    <div class="stats-info">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">出库申请单号</span>
                                    <div>
                                        <input class="form-control" id="stockoutapply_id" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">创建时间</span>
                                    <div>
                                        <input class="form-control" id="created_at" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon">状态</span>
                                    <div>
                                        <select class="form-control select2" id="stockoutapply_state" disabled></select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon" >出库申请人</span>
                                    <div>
                                        <input class="form-control" id="apply_applicant" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon" >审批人</span>
                                    <div>
                                        <input class="form-control" v-model="apply.apply_approver" readonly>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                </div>
                <h4 class="panel-title">出库申请单</h4>
            </div>
            <div class="panel-body">
                    <div class="form-inline" role="form">
                        <div class="form-group" style="margin-bottom: 10px">
                            <div class="form-group">
                                <input class="form-control"  id="search_text" placeholder="搜索物料编码、名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">

                            <button id="addMateriel" class="btn btn-info" v-on:click="addM">增加
                            </button>
                            <button id="saveApply" class="btn btn-info" v-on:click="saveInvalidateOrderMateriel" >提交</button>
                        </div>
                </div>
                <table id="mTable"></table>
            </div>
        </div>
        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增出库物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <input class="form-control"  id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称" v-on:input="getMateriel">
                        </div>
                        <div class="form-group col-xs-3">
                            <button type="button" class="btn btn-info" v-on:click="addInvalidateApply">保存</button>
                        </div>
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    import Constant from '../../../assets/js/constant'
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCAStockOutApplyControl?method='

    export default {
        data: function() {
            return {
                pagePara:{},
                apply:{},
                searchedRow:[],
                selectModelType: 2,
                show_return: 0
            }
        },
        name: 'NCAStockOutApplyDetailControl',
        mounted: function() {
            let _self = this;
            let $mTable = $('#mTable');
            let $table = $('#table');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'initApply', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                common.initSelect2($('#stockoutapply_state'), _self.pagePara.stockoutapplyState);
                common.initSelect2($('#materiel_unit'), _self.pagePara.unitInfo);
                _self.stockoutapply_id = common.getUrlParams('stockoutapplyId');
                _self.selectModelType = _self.pagePara.stockModelInfo[0].id
                response = await _self.$http.post(apiUrl + 'getStockOutApply', {stockoutapply_id:_self.stockoutapply_id});
                $('#stockoutapply_id').val(_self.stockoutapply_id).trigger('change');
                _self.apply = JSON.parse(JSON.stringify(response.data.info[0]));
                $('#created_at').val(_self.apply.created_at);
                $('#apply_applicant').val(_self.apply.apply_applicant);
                $('#stockoutapply_state').val(_self.apply.stockoutapply_state).trigger('change');
                if(_self.apply.stockoutapply_state == 1 || _self.apply.stockoutapply_state == 3){
                    init_mTable_DisAbled();
                }else{
                    init_mTable();
                }
                initTable();

                common.reSizeCall();
            }
            function queryParams(params) {
                params.stockoutapply_id=_self.stockoutapply_id;
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }

            function rFormatter(value, row) {
                return [
                    '<a class="tableDelete btn btn-info btn-xs m-r-5" title="删除">x</a>'
                ].join('')
            }
            let rowDelete = function(_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function() {
                    _self.$http.post(apiUrl + 'delete', row).then((response) => {
                        $('#tableT').bootstrapTable('remove', {
                            field: key,
                            values: [row[key]]
                        });
                        common.dealSuccessCommon('删除成功');
                        $('#mTable').bootstrapTable("refresh")
                        console.log('delete success');
                    }, (response) => {
                        console.log('delete error');
                        common.dealErrorCommon(_self, response);
                    });
                });
            };
            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    rowDelete(_self, '删除', apiUrl, row, 'stockoutapplydetail_id');
                }
            }
            function init_mTable() {
                $mTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_mtable',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '库存管理模式', 'stockModelInfo'),
                        common.BTRowFormatEditableRight('stockoutapplydetail_amount', '出库数量'),
                        common.actFormatter('act', rFormatter, tableEvents),
                    ],
                    idField:  'stockoutapplydetail_id',
                    uniqueId: 'stockoutapplydetail_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockoutapplydetail_id', $mTable)
                    }
                })
                common.changeTableClass($table)
            }

            function init_mTable_DisAbled() {
                $mTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_mtable',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '库存管理模式', 'stockModelInfo'),
                        common.BTRowFormatAlignRight('stockoutapplydetail_amount', '出库数量'),
                    ],
                    idField:  'stockoutapplydetail_id',
                    uniqueId: 'stockoutapplydetail_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockoutapplydetail_id', $mTable)
                    }
                })
                common.changeTableClass($table)
            }
            function queryParamsTable(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val()
                params.stock_model = _self.selectModelType;
                console.log(params)
                return JSON.stringify(params);
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchTable',
                    queryParams: queryParamsTable,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [{
                        field: 'state',
                        checkbox: true
                    },
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '库存管理模式', 'stockModelInfo'),
                    ],
                    idField: 'stockmap_id',
                    uniqueId: 'stockmap_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
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

            $(function() {
                initData();
                console.log('qwdwqdqwdwq')
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#mTable').bootstrapTable("refresh")
            },
            addM: function(event) {
                let _self = this;
                if(_self.apply.stockoutapply_state == 1){
                    return common.dealPromptCommon('该申请单已提交')
                }else if (_self.apply.stockoutapply_state == 3){
                    return common.dealPromptCommon('该申请单已通过')
                }
                $('#AddMaterielModal').modal('show');
                _self.searchedRow = [];
                $("#matNameOrCodeOrFormat").val('');
                $('#table').bootstrapTable("refresh");
            },
            getMateriel:function(event){
                let _self = this;
                $('#table').bootstrapTable("refresh")
            },
            addInvalidateApply:function(event){
                let _self = this;
                _self.apply = {};
                for (var i = 0; i < _self.searchedRow.length; i++) {
                    _self.apply.materiel_id = _self.searchedRow[i].materiel_id
                    _self.apply.materiel_manage = _self.searchedRow[i].materiel_manage
                    _self.apply.stockoutapply_id = _self.stockoutapply_id;
                    _self.$http.post(apiUrl + 'add_apply_order', _self.apply).then((response) => {
                        $('#table').bootstrapTable("refresh")
                        $('#mTable').bootstrapTable("refresh")
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
                $('#AddMaterielModal').modal('hide');
                $('#mTable').bootstrapTable("refresh")
                _self.searchedRow = []
            },
            saveInvalidateOrderMateriel:function(event){
                let _self = this;
                if(_self.apply.stockoutapply_state == 1){
                    return common.dealPromptCommon('该申请单已提交')
                }else if (_self.apply.stockoutapply_state == 3){
                    return common.dealPromptCommon('该申请单已通过')
                }
                let params={
                    stockoutapply_id:_self.stockoutapply_id
                }
                _self.$http.post(apiUrl + 'setTask', params).then((response) => {
                    let retData = response.data.info;
                    $('#mTable').bootstrapTable("refresh");
                    //$('#created_at').val(_self.apply.created_at).trigger('change');
                    _self.searchedRow=[];
                    common.dealSuccessCommon('提交成功');
                    $('#stockoutapply_state').val(retData.stockoutapply_state).trigger('change');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });

            }
        }
    }
</script>
<style scoped>
</style>
