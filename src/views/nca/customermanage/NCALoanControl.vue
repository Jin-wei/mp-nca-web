<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">客户管理</a></li>
            <li class="active">贷款列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">贷款列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索订单号、姓名、手机号、地址" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="LoanTable(1)">
                                <a href="#inloan" data-toggle="tab">
                                    <span>未受理</span>
                                </a>
                            </li>
                            <li class="" @click="LoanTable(7)">
                                <a href="#outloan" data-toggle="tab">
                                    <span>已受理</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="inloan">
                                <table id="inLoanTable"></table>
                            </div>
                            <div class="tab-pane fade" id="outloan" >
                                <table id="outLoanTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="OutModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">贷款</h4>
                    </div>
                    <form @submit.prevent="saveOut" id="formE">
                        <div class="modal-body row">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="name" disabled>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info"><i class="fa fa-fw fa-plus"></i>保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/customermanage/NCALoanControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                workRow:{},
                oldRow: ''
            }
        },
        name: 'NCALoanControl',
        mounted: function() {
            let _self = this;
            let $inLoanTable = $('#inLoanTable');
            let $outLoanTable = $('#outLoanTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }

            function operateFormatter(value, row, index) {
                if (_self.tableType == 1) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 accept_appointment">受理</a>'
                    ].join('')
                } else {
                    return ''
                }
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/ordermanage/NCAOrderDetailControl',
                        query: {
                            orderId: row.order_id
                        }
                    })

                },
                'click .accept_appointment': function (e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row);

                    $('#name').val(_self.oldRow.name);
                    $('#formE').parsley().reset()
                    $('#OutModal').modal('show');
                }
            };
            //未受理
            function queryParamsFit(params) {
                params.search_text=$('#search_text').val();
                params.loan_state = '0'
                return JSON.stringify(params)
            }
            function initFitOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号', nameFormatter,tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatWithFormatterAlignLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormatAlignRight('order_house_area', '面积'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'project_type', '工程类型', 'projectTypeInfo'),
                    common.BTRowFormatWithFormatterAlignRight('loan_amount', '贷款金额',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 'loan_state', '状态', 'stateType'),
                    common.BTRowFormat('created_at', '申请日期'),
                    common.actFormatter('act', operateFormatter, tableEvents)
                ];
                $inLoanTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsFit,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'loan_id',
                    uniqueId: 'loan_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_f', row, 'loan_id', $inLoanTable)
                    },
                    onPostBody: function () {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        });
                    }
                });
                common.changeTableClass($inLoanTable)
            }
            //已受理
            function queryParamsGroup(params) {
                params.search_text=$('#search_text').val();
                params.loan_state = '1'
                return JSON.stringify(params)
            }
            function initGroupOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号', nameFormatter,tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatWithFormatterAlignLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormatAlignRight('order_house_area', '面积'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'project_type', '工程类型', 'projectTypeInfo'),
                    common.BTRowFormatWithFormatterAlignRight('loan_amount', '贷款金额',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 'loan_state', '状态', 'stateType'),
                    common.BTRowFormat('created_at', '申请日期'),
                    common.actFormatter('act', operateFormatter, tableEvents)
                ];
                $outLoanTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsGroup,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'loan_id',
                    uniqueId: 'loan_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function () {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        });
                    }
                });
                common.changeTableClass($outLoanTable)
            }

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#project_type'), retData.projectTypeInfo);
                common.initSelect2($('#loan_state'), retData.stateType);

                initFitOrderTable();
                initGroupOrderTable();
                common.reSizeCall();
            }

            $(function() {
                initData();
            })
        },
        methods: {
            LoanTable:function(index){
                let _self = this;
                _self.tableType=index;

                if(index === 1 ){
                    $('#inLoanTable').bootstrapTable("refresh")
                } else if (index === 7 ){
                    $('#outLoanTable').bootstrapTable("refresh")
                }
            },
            queryConfirm: function(event) {
                let _self = this;
                if(_self.tableType === 1 ){
                    $('#inLoanTable').bootstrapTable("refresh")
                } else if (_self.tableType === 7 ){
                    $('#outLoanTable').bootstrapTable("refresh")
                }
            },
            saveOut: async function () {
                let _self = this;
                try {
                    if ($('#formE').parsley().isValid()) {
                        let params = {
                            loan_id: _self.oldRow.loan_id,
                            loan_state: 1
                        };

                        let result = await _self.$http.post(apiUrl + 'modify', params);

                        $('#OutModal').modal('hide');
                        $('#inLoanTable').bootstrapTable("refresh");
                        $('#formE').parsley().reset()
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        }
    }
</script>
<style scoped>
textarea{
    width:100%;resize:none
}
</style>
