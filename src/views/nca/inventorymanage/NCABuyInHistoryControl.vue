<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">库存管理</a></li>
        <li class="active">入库流水列表</li>
    </ol>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">入库流水列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="start_date" placeholder="开始时间"/>
                            </div>
                            <div class="form-group">
                                <span>-</span>
                                <input type="text" class="form-control" id="end_date" placeholder="结束时间"/>
                            </div>
                            <div class="form-group" v-if="showIndex==0">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="queryData.bs_order_id" placeholder="请输品质检验单号" style="width: 140px"/>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="queryData.bill_code" placeholder="请输入入库单号" style="width: 140px"/>
                                </div>
                                <div class="form-group">
                                    <input class="form-control" multiple id="search_text" v-model="queryData.supplier" placeholder="搜索供应商编号、供应商名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-if="showIndex==1">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="queryData.os_order_id" placeholder="请输入申请单号"/>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="queryData.other_bill_code" placeholder="请输入入库单号"/>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <ul class="nav nav-tabs" style="margin: 0px 15px">
                    <li class="active" @click="showTable(0)">
                        <a href="#buy_history" data-toggle="tab">
                            <span>采购入库流水</span>
                        </a>
                    </li>
                    <li class="" @click="showTable(1)">
                        <a href="#other_history" data-toggle="tab">
                            <span>其他入库流水</span>
                        </a>
                    </li>
                </ul>
                <div class="tab-content" style="padding: 15px">
                    <div class="tab-pane fade active in" id="buy_history">
                        <table id="historyTable"></table>
                    </div>
                    <div class="tab-pane fade" id="other_history" >
                        <table id="otherhistoryTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/inventorymanage/NCABuyInControl?method=';

export default {
    data: function() {
        return {
            masterTable: [],
            pagePara: {},
            queryData: {},
            showIndex:0
        }
    },
    name: 'NCABuyInHistoryControl',
    methods: {
        queryParams: function (params) {
            params.bs_order_id = this.queryData.bs_order_id;
            params.bill_code = this.queryData.bill_code;
            params.supplier = this.queryData.supplier;
            params.start_date = this.queryData.start_date;
            params.end_date = this.queryData.end_date;
            params.os_order_id = this.queryData.os_order_id;
            params.other_bill_code = this.queryData.other_bill_code;
            return JSON.stringify(params);
        },
        initHistoryTable: function () {
            let router = this.$router;
            let $historyTable = $('#historyTable');
            window.tableEvents = {
                'click .orderDetail': function(e, value, row, index) {
                    console.log('orderDetail:', row);
                    router.push({path: '/nca/inventorymanage/NCABuyInHistoryDetailControl', query: row});
                }
            };
            $historyTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getBuyInOrderHistory',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFW('created_at', '入库时间', value => {
                        return new Date(value).toLocaleString();
                    }),
                    common.BTRowFormat('bs_order_id', '品质检验单号'),
                    common.BTRowFormat('bill_code', '入库单号'),
                    common.BTRowFormatAlignLeft('supplier_name', '供应商'),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'warehouse_id', '仓库', 'wareHouseInfo'),
                    common.BTRowFormatAlignRight('done_count', '入库数量'),
                    common.BTRowFormat('ior_phone', '联系电话'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 orderDetail btn-info-delete">查看</a>';
                    }, tableEvents)
                ],
                sortOrder:'asc',
                idField: 'ior_id',
                uniqueId: 'ior_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {

                },
                onEditableSave: function(field, row, oldValue, $el) {
                    console.log('onEditableSave', row);
                }
            });
            common.changeTableClass($historyTable);
        },
        initOtherHistoryTable:function(event){
            let _self = this;
            let router = this.$router;
            let $otherhistoryTable = $('#otherhistoryTable');
            window.tableEvents = {
                'click .orderDetail': function(e, value, row, index) {
                    console.log('orderDetail:', row);
                    router.push({path: '/nca/inventorymanage/NCAOtherInHistoryDetailControl', query: row});
                }
            };

            $otherhistoryTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getOtherInOrderHistory',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFW('created_at', '入库时间', value => {
                        return new Date(value).toLocaleString();
                    }),
                    common.BTRowFormat('bs_order_id', '申请订单号'),
                    common.BTRowFormat('bill_code', '入库单号'),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'warehouse_id', '仓库', 'wareHouseInfo'),
                    common.BTRowFormatAlignRight('done_count', '入库数量'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 orderDetail">查看</a>';
                    }, tableEvents)
                ],
                idField: 'ior_id',
                uniqueId: 'ior_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {

                },
                onEditableSave: function(field, row, oldValue, $el) {

                }
            });
            common.changeTableClass($otherhistoryTable)
        },
        showTable: async function(index) {
            let _self = this;
            _self.showIndex = index;
            console.log('showTable:', index);
            if(index==0){
                $('#historyTable').bootstrapTable("refresh");
            }else if (index==1){
                $('#otherhistoryTable').bootstrapTable('refresh');
                await _self.initOtherHistoryTable()
            }
        },
        initPage: function () {
            common.initDatepicker($('#start_date'));
            common.initDatepicker($('#end_date'));

            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
                console.log('init success:', this.pagePara);
                this.initHistoryTable();
                this.initOtherHistoryTable();
            }, (response) => {
                console.log('init error');
                common.dealErrorCommon(this, response)
            });
        },
        queryConfirm: function () {
            this.queryData.start_date = $('#start_date').val();
            this.queryData.end_date = $('#end_date').val();
            console.log('queryConfirm:', this.queryData);
            $('#historyTable').bootstrapTable("refresh");
        },
        search: function () {
            this.queryData.start_date = $('#start_date').val();
            this.queryData.end_date = $('#end_date').val();
            $('#otherhistoryTable').bootstrapTable('destroy');
            this.initOtherHistoryTable()
        }
    },
    mounted: function() {
        this.masterTable[0] = $('#historyTable');
        this.masterTable[1] = $('#otherhistoryTable');
        this.initPage();
    }
}
</script>
<style scoped>

</style>
