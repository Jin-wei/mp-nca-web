<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">库存管理</a></li>
            <li class="active">入库任务列表</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">入库任务列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form" v-if="showIndex==0">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" multiple v-model="searchText" placeholder="品质检验单号" style="width: 250px;">
                                </div>
                                <div class="form-group">
                                    <label>&emsp;订单状态</label>
                                    <select class="form-control select-width" id="store_type" v-model="typeValue">
                                        <option v-for="type in pagePara.storeTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" @click="showTable(0)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                        <div class="form-inline" role="form" v-if="showIndex==1">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="searchOrder" placeholder="搜索入库申请单号" id="search_textM" style="width: 180px">
                                </div>
                                <div class="form-group">
                                    <label>&emsp;订单状态</label>
                                    <select class="form-control select-width" v-model="OedertypeValue">
                                        <option v-for="type in pagePara.TypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="showTable(1)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                        <div class="form-inline" role="form" v-if="showIndex==2">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="searchProduct" placeholder="搜索生产任务单号" style="width: 250px">
                                </div>
                                <div class="form-group">
                                    <label>&emsp;入库状态</label>
                                    <select class="form-control select-width" v-model="productInState">
                                        <option v-for="type in pagePara.storeTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="showTable(2)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <!--<div class="panel-body auto-height hidedesk">-->
                    <ul class="nav nav-tabs" style="margin: 0px 15px">
                        <li class="active" @click="showTable(0)">
                            <a href="#buy_in" data-toggle="tab">
                                <span>采购入库管理</span>
                            </a>
                        </li>
                        <li @click="showTable(2)">
                            <a href="#product_in" data-toggle="tab">
                                <span>产品入库</span>
                            </a>
                        </li>
                        <li class="" @click="showTable(1)">
                            <a href="#other_in" data-toggle="tab">
                                <span>其他入库管理</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content" style="padding: 15px">
                        <div class="tab-pane fade active in" id="buy_in">
                            <table id="buyInTable"></table>
                        </div>
                        <div class="tab-pane fade active in" id="product_in">
                            <table id="productInTable"></table>
                        </div>
                        <div class="tab-pane fade" id="other_in" >
                            <table id="otherInTable"></table>
                        </div>
                    </div>
                    <!--</div>-->
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
                pagePara: {},
                masterTable: [],
                searchText: null,
                searchOrder: null,
                typeValue: 0,
                productInState: 0,
                OedertypeValue: 0,
                showIndex:0,
                searchProduct: ''
            }
        },
        name: 'NCABuyInControl',
        methods: {
            queryParams: function (params) {
                params.search_keyword = this.searchText;
                params.search_type = this.typeValue;
                params.search_order = this.searchOrder;
                params.search_Otype = this.OedertypeValue
                return JSON.stringify(params);
            },
            queryParamsOther: function (params) {
                params.search_keyword = this.searchText;
                params.search_type = this.typeValue;
                params.search_order = this.searchOrder;
                params.search_Otype = this.OedertypeValue
                return JSON.stringify(params);
            },
            queryParamsProduct: function (params) {
                params.productivetask_code = this.searchProduct;
                params.stock_in_state = this.productInState;
                return JSON.stringify(params);
            },
            search: function(event) {
                this.masterTable.bootstrapTable("refresh", this.queryParams);
            },
            initPage: async function () {
                await this.$http.post(apiUrl + 'init', {}).then(response => {
                    this.pagePara = response.data.info;
                    console.log('init success');
                }, (response) => {
                    console.log('init error');
                    common.dealErrorCommon(this, response)
                });
                this.initTable(0);
                this.initTableOtherMateriel;
            },
            showTable: async function(index) {
                let _self = this;
                _self.showIndex = index;
                console.log('showTable:', index);
                if(index==0){
                    $('#buyInTable').bootstrapTable("refresh");
                }else if (index==1){
                    $('#otherInTable').bootstrapTable('refresh');
                    await _self.initTableOtherMateriel()
                } else if (index == 2) {
                    $('#productInTable').bootstrapTable("refresh");
                    await _self.initProductInTable()
                }

            },
            initTable: function (event) {
                let _self = this;
                let $buyInTable = $('#buyInTable');
                window.tableEvents = {
                    'click .process': (e, value, row, index) => {
                        this.$router.push({path: '/nca/inventorymanage/NCABuyInDetailControl', query: row});
                    }
                };
                $buyInTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getQualityCheckOrder',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('qualitycheck_id', '品质检验单'),
                        common.BTRowFormatAlignLeft('name', '质检人'),
                        common.BTRowFormatEdSelect2Disabled(this, 'order_status', '入库状态', 'TypeInfo'),
                        common.actFormatter('act', () => {
                            return '<a class="btn btn-info btn-xs m-r-5 process">入库</a>';
                        }, tableEvents)
                    ],
                    sortOrder:'asc',
                    idField: 'qualitycheck_id',
                    uniqueId: 'qualitycheck_id',
                    toolbar: '#toolbar',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageNumber: 1,
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {

                    },
                    onEditableSave: (field, row, oldValue, $el) => {

                    }
                });
                common.changeTableClass($buyInTable);
            },
            initTableOtherMateriel:function(event){
                let _self = this;
                let $otherInTable = $('#otherInTable');
                window.tableEvents = {
                    'click .stock': (e, value, row, index) => {
                        this.$router.push({path: '/nca/inventorymanage/NCAOtherInDetailControl', query: row});
                    }
                };

                $otherInTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getOtherInOrder',
                    queryParams: this.queryParamsOther,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns:  [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('stockapply_id', '入库申请单号'),
                        common.BTRowFormatAlignLeft('otherstock_approver', '审批人'),
                        common.BTRowFormatEdSelect2Disabled(this, 'otherstock_state', '入库状态', 'TypeInfo'),
                        common.actFormatter('act', () => {
                            return '<a class="btn btn-info btn-xs m-r-5 stock">入库</a>';
                        }, tableEvents)
                    ],
                    idField: 'otherstock_id',
                    uniqueId: 'otherstock_id',
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
                common.changeTableClass($otherInTable)
            },
          initProductInTable:function(){
            let _self = this;
            let $productInTable = $('#productInTable');
            window.tableEvents = {
              'click .stock': (e, value, row, index) => {
                this.$router.push({path: '/nca/inventorymanage/NCAProductInItemsControl', query: row});
              }
            };

            $productInTable.bootstrapTable({
              method: 'POST',
              url: apiUrl + 'getProductInOrder',
              queryParams: this.queryParamsProduct,
              sidePagination: 'server',
              ajaxOptions: common.bootstrapTableAjaxOtions,
              responseHandler: function(res) {
                return res.info;
              },
              height: common.getTableHeight(),
              columns:  [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('productivetask_code', '生产任务单号'),
                common.BTRowFormat('order_id', '销售订单号'),
                common.BTRowFormatAlignLeft('name', '客户名称'),
                common.BTRowFormatEdSelect2Disabled(this, 'stock_in_state', '入库状态', 'TypeInfo'),
                common.actFormatter('act', () => {
                  return '<a class="btn btn-info btn-xs m-r-5 stock">入库</a>';
                }, tableEvents)
              ],
              idField: 'productivetask_id',
              uniqueId: 'productivetask_id',
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
            common.changeTableClass($productInTable)
          }

        },
        mounted: function() {
            this.masterTable[0] = $('#buyInTable');
            this.masterTable[1] = $('#otherInTable');
            this.initPage();
            this.initTable();
        }
    }
</script>
<style scoped>
    .select-width {
        width: 100px;
    }
</style>
