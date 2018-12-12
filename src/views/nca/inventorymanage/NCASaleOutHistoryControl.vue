<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">库存管理</a></li>
            <li class="active">出库流水列表</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">出库流水列表</h4>
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
                                        <input type="text" class="form-control" id="bs_order_id" placeholder="请输入销售订单号"/>
                                    </div>
                                    <div class="form-group">
                                        <input type="text" class="form-control" id="bill_code" placeholder="请输入出库单号"/>
                                    </div>
                                    <div class="form-group">
                                        <input class="form-control" multiple id="user_name" placeholder="搜索客户名称" style="width: 150px;">
                                    </div>
                                </div>
                                <div class="form-group" v-if="showIndex==1">
                                    <div class="form-group">
                                        <input type="text" class="form-control" id="other_out_apply_id" placeholder="请输入申请单号"/>
                                    </div>
                                    <div class="form-group">
                                        <input type="text" class="form-control" id="other_out_bill_code" placeholder="请输入出库单号"/>
                                    </div>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(0)">
                                <a href="#sale_out" data-toggle="tab">
                                    <span>销售出库流水</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(1)">
                                <a href="#other_out" data-toggle="tab">
                                    <span>其他出库流水</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="sale_out">
                                <table id="historyTable"></table>
                            </div>
                            <div class="tab-pane fade" id="other_out" >
                                <table id="otherOutHistoryTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCASaleOutControl?method=';

    export default {
        data: function() {
            return {
                masterTable: {},
                pagePara: {},
                queryData: {},
                showIndex:0
            }
        },
        name: 'NCASaleOutHistoryControl',
        methods: {
            queryParams: function (params) {
              if (this.showIndex == 0) {
                params.bs_order_id = $('#bs_order_id').val();
                params.bill_code = $('#bill_code').val();
                params.user_name = $('#user_name').val();
              } else {
                params.order_id = $('#other_out_apply_id').val();
                params.bill_code = $('#other_out_bill_code').val();
              }
                params.start_date = $('#start_date').val();
                params.end_date = $('#end_date').val();
                return JSON.stringify(params);
            },
            initHistoryTable: function () {
                let router = this.$router;
                let $historyTable = $('#historyTable');
                window.tableEvents = {
                    'click .orderDetail': function(e, value, row, index) {
                        console.log('orderDetail:', row);
                        router.push({path: '/nca/inventorymanage/NCASaleOutHistoryDetailControl', query: row});
                    }
                };
                $historyTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSaleOutOrderHistory',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFW('created_at', '出库时间', value => {
                            return new Date(value).toLocaleString();
                        }),
                        common.BTRowFormat('bs_order_id', '销售订单号'),
                        common.BTRowFormat('bill_code', '出库单号'),
                        common.BTRowFormatAlignLeft('name', '客户名称'),
                        common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'warehouse_id', '仓库', 'wareHouseInfo'),
                        common.BTRowFormatAlignRight('done_count', '出库数量'),
                        common.BTRowFormat('ior_phone', '联系电话'),
                        common.actFormatter('act', () => {
                            return '<a class="btn btn-info btn-xs m-r-5 orderDetail">查看</a>';
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
          initOtherOutHistoryTable:function(){
            let _self = this;
            let router = this.$router;
            let $otherOutHistoryTable = $('#otherOutHistoryTable');
            window.tableEvents = {
              'click .orderDetail': function(e, value, row, index) {
                router.push({path: '/nca/inventorymanage/NCAOtherOutHistoryDetailControl', query: row});
              }
            };

            $otherOutHistoryTable.bootstrapTable({
              method: 'POST',
              url: apiUrl + 'getOtherOutOrderHistory',
              queryParams: this.queryParams,
              sidePagination: 'server',
              ajaxOptions: common.bootstrapTableAjaxOtions,
              responseHandler: function(res) {
                return res.info;
              },
              height: common.getTableHeight(),
              columns:  [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFW('created_at', '出库时间', value => {
                  return new Date(value).toLocaleString();
                }),
                common.BTRowFormat('order_id', '申请订单号'),
                common.BTRowFormat('bill_code', '出库单号'),
                common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'warehouse_id', '仓库', 'wareHouseInfo'),
                common.BTRowFormatAlignRight('amount', '出库数量'),
                common.actFormatter('act', () => {
                  return '<a class="btn btn-info btn-xs m-r-5 orderDetail">查看</a>';
                }, tableEvents)
              ],
              idField: 'inventoryaccount_id',
              uniqueId: 'inventoryaccount_id',
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
            common.changeTableClass($otherOutHistoryTable)
          },
          showTable: async function(index) {
            let _self = this;
            _self.showIndex = index;
            if(index==0){
              $('#historyTable').bootstrapTable("refresh");
            }else if (index==1){
              $('#otherOutHistoryTable').bootstrapTable('refresh');
            }
          },
            initPage: function () {
                common.initDatepicker($('#start_date'));
                common.initDatepicker($('#end_date'));

                this.$http.post(apiUrl + 'init', {}).then(response => {
                    this.pagePara = response.data.info;
                    console.log('init success:', this.pagePara);
                    this.initHistoryTable();
                    this.initOtherOutHistoryTable();
                }, (response) => {
                    console.log('init error');
                    common.dealErrorCommon(this, response)
                });
            },
            queryConfirm: function () {
              if(this.showIndex == 0){
                $('#historyTable').bootstrapTable("refresh");
              }else if (this.showIndex == 1){
                $('#otherOutHistoryTable').bootstrapTable('refresh');
              }
            }
        },
        mounted: function() {
            this.initPage();
        }
    }
</script>
<style scoped>

</style>
