<template>
    <div>
        <!-- begin breadcrumb -->
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
                                <div class="form-group">
                                    <input class="form-control" id="search_text3" placeholder="搜索订单号" style="width: 230px;">
                                </div>
                                <!--<div class="form-group">-->
                                    <!--<select class="select2 form-control select-width" id="sales_type" style="width: 150px;">-->
                                        <!--<option value="">请选择订单状态</option>-->
                                        <!--<option v-for="state in pagePara.prodeuceInfo" v-bind:value="state.id">{{state.text}}</option>-->
                                    <!--</select>-->
                                <!--</div>-->
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="panel-body">
                        <table id="salesOrderTable"></table>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

export default {
    props: ['corporate', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {}
        }
    },
    name: 'NCASalesOrderInformationControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        corporate: function() {
            let _self = this;
            let $salesOrderTable = $('#salesOrderTable');
            common.initSelect2($('#order_state'), this.pagePara.orderStateSalesInfo);

            function queryParams(params) {
                params.search_text=$('#search_text3').val();
                // params.sales_type=$('#sales_type').val();
                params.corporateclients_id =_self.corporate.corporateclients_id;
                return JSON.stringify(params);
            }

            function initTable() {
                $salesOrderTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_a',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: 300,
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('order_id', '订单号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '订单状态', 'orderStateSalesInfo'),
                        common.BTRowFormat('create_date', '创建日期'),
                    ],
                    idField: 'corporateclients_id',
                    uniqueId: 'corporateclients_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($salesOrderTable)
            }
            $(function() {
                initTable();
            })
        }
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#salesOrderTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
