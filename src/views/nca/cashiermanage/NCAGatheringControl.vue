<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">出纳管理</a></li>
            <li class="active">收款申请列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收款申请列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text" placeholder="搜索收款名称、来款单位名称" style="width: 230px;">
                                    </div>
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body" >
                        <div class="tab-pane fade active in" id="set_table">
                            <table id="setTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增收款申报记录</h4>
                    </div>
                    <form @submit.prevent="saveSaleOrder" id="formC">
                        <div class="modal-body">

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>收款项名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="cashiergathering_name" data-parsley-group="block-1" v-model="workRow.cashiergathering_name" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>收款项类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control"  id="cashiergathering_type" data-parsley-group="block-1" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required"></span>客户编号</label>
                                    <div class="col-sm-8">
                                        <select class="form-control"  id="cashiergathering_customer_code"
                                        data-parsley-group="block-1"
                                        v-bind:disabled="isReadOnlySearch"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required"></span>收款对象名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="cashiergathering_source_name"
                                               data-parsley-group="block-1"
                                               v-bind:readonly="isReadOnlyInput" v-model ='workRow.cashiergathering_source_name'>
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required"></span>收款时间</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="cashiergathering_cashier_time" />
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required"></span>联系电话</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="cashiergathering_phone" data-parsley-group="block-1">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>收款金额</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="cashiergathering_gathering_money" data-parsley-group="block-1"  data-parsley-required="true">
                                    </div>
                                </div>
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-4 control-label"><span class="table-required"></span>收款人</label>-->
                                    <!--<div class="col-sm-8">-->
                                        <!--<input class="form-control" id="cashiergathering_cashier" data-parsley-group="block-1" >-->
                                    <!--</div>-->
                                <!--</div>-->
                            </div>
                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" style="width: 447px;" id="cashiergathering_remark" maxlength="100" data-parsley-maxlength="100"></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveAct">提交</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/cashiermanage/NCAGatheringControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                workRow:{},
                isReadOnlySearch:true,
                isReadOnlyInput:true,
                cashiergathering_id:''
            }
        },
        name: 'NCAGatheringControl',
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');

            _self.cashiergathering_id = _self.$route.query.cashiergathering_id;
            _self.cashiergathering_code = _self.$route.query.cashiergathering_code;
            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                if(_self.cashiergathering_id){
                    params.cashiergathering_id=_self.cashiergathering_id;
                }
                if(_self.cashiergathering_code){
                    params.cashiergathering_code=_self.cashiergathering_code;
                }
                return JSON.stringify(params)
            }
            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('cashiergathering_code', '项目编码'),
                    common.BTRowFormatAlignLeft('cashiergathering_name', '收款项名称'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'cashiergathering_type', '收款项类型', 'gathering_type'),
//                    common.BTRowFormat('cashiergathering_customer_code', '客户编号'),
                    common.BTRowFormatAlignLeft('cashiergathering_source_name', '来款单位名称'),
                    common.BTRowFormatWithFormatterAlignRight('cashiergathering_gathering_money', '收款金额',common.priceFormat),
                    common.BTRowFormat('cashiergathering_phone', '联系电话'),
//                    common.BTRowFormat('cashier_name', '收款人'),
                    common.BTRowFormat('cashiergathering_cashier_time', '收款时间'),
                    common.BTRowFormatWithFormatterAlignLeft('cashiergathering_remark', '备注',common.remarkFormatter),
                    common.BTRowFormatAlignLeft('declarant_name', '申报人'),
                    common.BTRowFormat('created_at', '申报时间'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'cashiergathering_state', '状态', 'cashiergathering_state'),
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'cashiergathering_id',
                    uniqueId: 'cashiergathering_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                common.initSelect2($('#cashiergathering_type'), retData.gathering_type);
                common.initSelect2($('#cashiergathering_customer_code'), retData.corporateclients);
                common.initDatepicker($('#cashiergathering_cashier_time'));
                initsetTable();
                $('#formC').parsley();

                $('#cashiergathering_type').on('select2:select', async function(evt) {
                    if(common.getSelect2Val("cashiergathering_type") == 0){
                        _self.isReadOnlySearch = false;
                        _self.isReadOnlyInput = true
                    }else{
                        _self.isReadOnlySearch = true;
                        _self.isReadOnlyInput = false
                    }
                })
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            },

            addM: function(event) {
                let _self = this;
                $('#cashiergathering_cashier_time').val('');
                $('#cashiergathering_name').val('');
                $('#cashiergathering_gathering_money').val('');
                $('#cashiergathering_phone').val('');
//                $('#cashiergathering_cashier').val('');
                $('#cashiergathering_remark').val('');
                $('#cashiergathering_type').val(null).trigger('change');
                $('#cashiergathering_customer_code').val(null).trigger('change');
                $('#AddModal').modal('show');
            },
            saveSaleOrder: async function(event) {
                let _self = this;
                let formC = $('#formC');
                if (formC.parsley().isValid()) {
                    try {
                        let params = {};
                        if(common.getSelect2Val("cashiergathering_type") == 0){
                            params.cashiergathering_customer_code = common.getSelect2Val('cashiergathering_customer_code');
                            params.cashiergathering_source_name = $('#cashiergathering_customer_code option:selected').text();
                        }else{
                            params.cashiergathering_customer_code ='';
                            params.cashiergathering_source_name = _self.workRow.cashiergathering_source_name;
                        }
                        params.cashiergathering_type = common.getSelect2Val('cashiergathering_type');
                        params.cashiergathering_cashier_time = $('#cashiergathering_cashier_time').val()?$('#cashiergathering_cashier_time').val():null;
                        params.cashiergathering_name = $('#cashiergathering_name').val();
                        params.cashiergathering_gathering_money = $('#cashiergathering_gathering_money').val();
                        params.cashiergathering_phone = $('#cashiergathering_phone').val();
//                        params.cashiergathering_cashier = $('#cashiergathering_cashier').val();
                        params.cashiergathering_remark = $('#cashiergathering_remark').val();

                        console.log('params',params);
                        let response = await _self.$http.post(apiUrl + 'add', params);
                        $('#AddModal').modal('hide');
                        $('#setTable').bootstrapTable("refresh");
                        formC.parsley().reset();
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
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
