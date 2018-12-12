<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">出纳管理</a></li>
            <li class="active">收款申报列表</li>
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
                        <h4 class="panel-title">收款申报列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索收款名称、来款单位名称" style="width: 230px;">
                                </div>
                            </div>

                            <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>

                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="tab-content">
            <div class="tab-pane fade active in" id="set_table">
                <table id="setTable"></table>
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
                cashiergathering_id:'',
                show_return: 0
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
    button{
        border: none;
        color: #777;;
        margin-left: 3em;
    }
</style>
