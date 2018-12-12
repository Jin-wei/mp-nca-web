<template>
    <div>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">销售订单管理</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索订单号" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="order_state_fit">
                                    <option value="">请选择订单状态</option>
                                    <option v-for="state in pagePara.orderStateSalesInfo" v-bind:value="state.id">{{state.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="table1"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/baseconfig/NCABusinessCustomerControl?method=';

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                oldRow: '',
            }
        },
        name: 'NCABusinessSaleOrderControl',
        mounted: function() {
            let _self = this
            let $table1 = $('#table1')
            _self.workRow = _self.$route.query;

            function queryParams(params) {
                params.domain_id = _self.workRow.domain_id;
                params.search_text = $('#search_text').val();
                params.order_state =$('#order_state_fit').val();
                return JSON.stringify(params)
            }
            function timeFormatter(value, row) {
                if (!value) return '';
                let date = value;
                date = date.split('T');
                let time = date[1].slice(0, 8);
                return date[0]
            }

            function initTable() {
                $table1.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_sale',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('order_id', '订单号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '订单状态', 'orderStateSalesInfo'),
                        //common.BTRowFormat('created_at', '创建日期'),
                        common.BTRowFormatWithFormatter('created_at', '创建日期', timeFormatter),
                    ],
                    idField: 'order_id',
                    uniqueId: 'order_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                })
                common.changeTableClass($table1)
            }

            async function initData() {
                await _self.getPagePara()
                common.initSelect2($('#order_state'), _self.pagePara.orderStateSalesInfo)
                initTable();
                console.log('dasda')
                common.reSizeCall();
            }
            initData()
        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            search: function(event) {
                $('#table1').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
