<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">其他入库申请</a></li>
            <li class="active">入库申请单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 col-sm-12">
                <div class="widget widget-stats bg-black-lighter">
                    <div class="stats-info">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">入库申请单</span>
                                    <div>
                                        <input class="form-control" v-model="apply.stockapply_id" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">创建时间</span>
                                    <div>
                                        <input class="form-control" v-model="apply.created_at" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon">状态</span>
                                    <div>
                                        <select class="form-control select2" id="apply_state" disabled></select>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon">申请人</span>
                                    <div>
                                        <input class="form-control" v-model="apply.apply_submit" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon" >审批人</span>
                                    <div>
                                        <input class="form-control" v-model="apply.apply_review" readonly>
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
                <h4 class="panel-title">其他入库申请单</h4>
            </div>
            <div class="panel-body">
                <div class="form-inline" role="form">
                    <div class="form-group" style="margin-bottom: 10px">
                        <div class="form-group">
                            <input class="form-control"  id="search_text" placeholder="搜索物料编码,物料名称" style="width: 230px;">
                        </div>
                        <span v-if = !stockapplyId>
                        <div class="form-group ">
                            <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                        </span>
                    </div>
                    <span v-if = !stockapplyId>
                        <div class="form-group pull-right">
                            <button id="addMateriel" class="btn btn-info" v-on:click="addM">增加
                            </button>
                            <button id="saveApply" class="btn btn-info" v-on:click="saveApplyMateriel" >提交</button>
                        </div>
                    </span>
                </div>
                <div>
                    <table id="stockDetailTable"></table>
                </div>
            </div>
        </div>


        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <input class="form-control"  id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="getMateriel">
                        </div>
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <button type="button" class="btn btn-info" v-on:click="addPuchaseApplyDetail">保存</button>
                        </div>
                        <table id="tableSearch"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCAStcokInApplyControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{},
                stockapply_id:'',
                apply:{},
                searchedRow:[],
                stockapplyId:'',
                show_return: 0
            }
        },
        name: 'NCAStockInApplyDetailControl',
        mounted: function() {
            let _self = this;
            let $stockDetailTable = $('#stockDetailTable');
            let $tableSearch = $('#tableSearch');
            _self.stockapplyId = _self.$route.query.stockapplyId

            async function initData() {

                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                common.initSelect2($('#apply_state'), _self.pagePara.purchaseApplyType);
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)

                _self.stockapply_id = common.getUrlParams('stockapply_id');
                if (_self.stockapply_id == null) {
                    _self.stockapply_id = _self.$route.query.stockapplyId
                }
                response = await _self.$http.post(apiUrl + 'getStockApply', {stockapply_id:_self.stockapply_id});
                _self.apply = JSON.parse(JSON.stringify(response.data.info[0]));
                $('#apply_state').val(_self.apply.apply_state).trigger('change');

                initAllDemandTable();
                initTableMateriel();
                common.reSizeCall();
            }
            function initTableNum(value, row) {
                return 0
            }
            function queryParams(params) {
                params.stockapply_id=_self.stockapply_id;
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initAllDemandTable() {
                window.tableEvents = {
                    'click .delete_stockApplyDetail': function(e, value, row, index) {
                        if(_self.apply.apply_state==1){
                            return common.dealPromptCommon('该申请单待审批')
                        } else if (_self.apply.apply_state==3){
                            return common.dealPromptCommon('该申请单已通过')
                        }
                        common.rowDeleteWithApi(_self, '入库申请单详情删除', apiUrl + 'deleteStockApplyDetail', $stockDetailTable, row, 'stockapplyitem_id', function() {})
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEditableRight('apply_amount', '数量'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('stock_remarks', '备注',120,7),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 delete_stockApplyDetail">删除</a>';
                    }, tableEvents)
                ];
                $stockDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getStockApplyDetail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'stockapplyitem_id',
                    uniqueId: 'stockapplyitem_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockapplyitem_id', $stockDetailTable)
                    }
                });
                common.changeTableClass($stockDetailTable)
            }
            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                return JSON.stringify(params)
            }
            function initTableMateriel() {
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getStockMateiel',
                    queryParams: queryParamsMateriel,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        {
                            field: 'state',
                            checkbox: true
                        },
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEnumberWMinALginRight('apply_amount', '默认数量'),
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10,15,25,50,100],
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
                    }
                });
                common.changeTableClass($tableSearch)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#stockDetailTable').bootstrapTable("refresh")
            },
            addM: function(event) {
                let _self = this;
                if(_self.apply.apply_state==1){
                    return common.dealPromptCommon('该申请单待审批')
                }else if (_self.apply.apply_state==3){
                    return common.dealPromptCommon('该申请单已通过')
                }
                $('#AddMaterielModal').modal('show');
                _self.searchedRow = [];
                $("#matNameOrCodeOrFormat").val('');
                $('#tableSearch').bootstrapTable("refresh");
                $("input[type=checkbox]").each(function() {
                    $(this).prop("checked", false);
                })
            },
            getMateriel:function(event){
                let _self = this;
                $('#tableSearch').bootstrapTable("refresh")
            },
            addPuchaseApplyDetail:function(event){
                let _self = this;
                let params=[];

                for(let s of _self.searchedRow){
                    if(s.apply_amount && s.apply_amount!=0){
                        params.push({
                            stockapply_id:_self.stockapply_id,
                            materiel_id:s.materiel_id,
                            apply_amount:s.apply_amount
                        })
                    } else {
                        return common.dealPromptCommon('请填写默认数量')
                    }
                }
                _self.$http.post(apiUrl + 'addStockApplyDetail', params).then((response) => {
                    let retData = response.data.info;
                    $('#stockDetailTable').bootstrapTable("refresh");
                    _self.searchedRow=[];
                    $('#AddMaterielModal').modal('hide');
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });

                $('#AddModal').modal('hide')
            },
            saveApplyMateriel:function(event){
                let _self = this;
                if(_self.apply.apply_state==1){
                    return common.dealPromptCommon('该申请单待审批')
                }else if (_self.apply.apply_state==3){
                    return common.dealPromptCommon('该申请单已通过')
                }
                let tableData=$('#stockDetailTable').bootstrapTable('getData');
                if(tableData.length===0){
                    return  common.dealWarningCommon('请先增加数据！');
                }
                let params={
                    stockapply_id:_self.stockapply_id
                }
                _self.$http.post(apiUrl + 'setTask', params).then((response) => {
                    let retData = response.data.info;
                    $('#tableSearch').bootstrapTable("refresh");
                    _self.searchedRow=[];
                    common.dealSuccessCommon('提交成功');
                    window.history.back()
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            }
        }
    }
</script>
<style scoped>
</style>