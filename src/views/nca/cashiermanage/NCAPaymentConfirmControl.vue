<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">出纳管理</a></li>
            <li class="active">付款列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs"  v-show="show_return == 1"  href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">付款列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索支出对象名称" style="width: 230px;">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-pane fade active in" id="set_table">
                            <table id="setTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/cashiermanage/NCAPaymentConfirmControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                paymentconfirm_id:'',
                show_return: 0
            }
        },
        name: 'NCAPaymentConfirmControl',
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');
            _self.paymentconfirm_id = _self.$route.query.paymentconfirm_id;
            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                params.paymentconfirm_id=_self.paymentconfirm_id;
                return JSON.stringify(params)
            }
            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    if(row.paymentconfirm_name==1){
                        _self.$router.push({
                            path: '/nca/baseconfig/NCASpecialExpenseControl',
                            query: {
                                sExpenseCode: row.paymentconfirm_source_code
                            }
                        })
                    }else{
                        _self.$router.push({
                            path: '/nca/baseconfig/NCATransReceptionExpenseDetailControl',
                            query: {
                                trExpenseCode: row.paymentconfirm_source_code
                            }
                        })
                    }
                }
            }
            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'paymentconfirm_name', '付款类型名称', 'payment_confirm_type'),
                    common.BTRowFormatWithFE('paymentconfirm_source_code', '单号', nameFormatter,tableEvents),
                    common.BTRowFormatWithFormatterAlignRight('paymentconfirm_money', '付款金额',common.priceFormat),
                    common.BTRowFormatAlignLeft('expend_name', '支出对象名称'),
                    common.BTRowFormatAlignLeft('declarant_name', '申报人'),
                    common.BTRowFormat('paymentconfirm_declarant_time', '申报时间'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'paymentconfirm_state', '状态', 'payment_confirm_state'),
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
                    idField: 'paymentconfirm_id',
                    uniqueId: 'paymentconfirm_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                initsetTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
