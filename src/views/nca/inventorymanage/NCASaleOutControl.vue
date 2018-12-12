<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">库存管理</a></li>
        <li class="active">出库列表</li>
    </ol>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">出库列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form" v-if="showIndex==0">
                        <!--<div class="form-inline" role="form">-->
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索销售订单号" style="width: 250px;">
                            </div>

                            <div class="form-group">
                                <label>&emsp;订单状态</label>
                                <select class="form-control select-width" v-model="typeValue">
                                    <option v-for="type in pagePara.storeTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>

                            <div class="form-group">
                                <label>&emsp;订单类型</label>
                                <select class="form-control select-width" v-model="orderType">
                                    <option v-for="type in pagePara.orderTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                        <!--</div>-->
                    </div>
                    <div class="form-inline" role="form" v-if="showIndex==1">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" v-model="searchOrder" placeholder="搜索出库申请单号" id="search_textM" style="width: 180px">
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
                                <label>&emsp;出库状态</label>
                                <select class="form-control select-width" v-model="productOutState">
                                    <option v-for="type in pagePara.storeTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="showTable(2)"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                </div>
                <ul class="nav nav-tabs" style="margin-left: 15px;margin-right: 15px">
                    <li class="active" @click="showTable(0)">
                        <a href="#buy_in" data-toggle="tab">
                            <span>销售出库管理</span>
                        </a>
                    </li>
                    <li @click="showTable(2)">
                        <a href="#product_out" data-toggle="tab">
                            <span>生产领料</span>
                        </a>
                    </li>
                    <li class="" @click="showTable(1)">
                        <a href="#other_in" data-toggle="tab">
                            <span>其他出库管理</span>
                        </a>
                    </li>
                </ul>
                <div class="tab-content" style="padding: 15px">
                    <div class="tab-pane fade active in" id="buy_in">
                        <table id="saleOutTable"></table>
                    </div>
                    <div class="tab-pane fade in" id="product_out">
                        <table id="productOutTable"></table>
                    </div>
                    <div class="tab-pane fade" id="other_in" >
                        <table id="otherOutTable"></table>
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
            tableIndex: 0,
            masterTable: [],
            pagePara: {},
            searchText: null,
            searchOrder: null,
            typeValue: 0,
            productOutState: 0,
            orderType: 0,
            showIndex:0,
            searchProduct: ''
        }
    },
    name: 'NCASaleOutControl',
    methods: {
        queryParams: function (params) {
            params.search_keyword = this.searchText;
            params.search_type = this.typeValue;
            params.orderType = this.orderType;
            params.search_order = this.searchOrder;
            return JSON.stringify(params);
        },
      queryParamsProduct: function (params) {
        params.productivetask_code = this.searchProduct;
        params.stock_out_state = this.productOutState;
        return JSON.stringify(params);
      },
        search: function() {
            this.masterTable[this.tableIndex].bootstrapTable("refresh", this.queryParams)
        },
        switchSaleOutTable: function (index) {
            this.searchText = null;
            this.typeValue = 0;
            this.tableIndex = index;
            this.search();
        },
        showTable: async function(index) {
            let _self = this;
            _self.showIndex = index;
            if(index==0){
                $('#saleOutTable').bootstrapTable("refresh");
            }else if (index==1){
                $('#otherOutTable').bootstrapTable('destroy');
                await _self.initTableOtherOutMateriel()
            } else if (index == 2) {
              $('#productOutTable').bootstrapTable("refresh");
              await _self.initProductOutTable()
            }
        },
        initTable: function (index) {
            let router = this.$router;
            window.tableEvents = {
                'click .saleOut': (e, value, row, index) => {
                    router.push({path: '/nca/inventorymanage/NCASaleOutDetailControl', query: row});
                }
            };
            this.masterTable[index].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getSaleOutOrder',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('order_id', '销售订单号'),
                    common.BTRowFormatAlignLeft('custom_name', '客户名称'),
                    common.BTRowFormatEdSelect2Disabled(this, 'order_status', '出库状态', 'storeTypeInfo'),
//                    common.BTRowFormat('custom_code', '客户编码'),
                    common.actFormatter('act', () => {
                        return `<a class="btn btn-info btn-xs m-r-5 saleOut">出库</a>`;
                    }, tableEvents)
                ],
                sortOrder:'asc',
                idField: 'order_id',
                uniqueId: 'order_id',
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
            common.changeTableClass(this.masterTable[index]);
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
            this.initTableOtherOutMateriel;
        },
        initTableOtherOutMateriel:function(event){
            let _self = this;
            let $otherOutTable = $('#otherOutTable');
            window.tableEvents = {
                'click .stock': (e, value, row, index) => {
                    this.$router.push({path: '/nca/inventorymanage/NCAOtherOutDetailControl', query: row});
                }
            };

            $otherOutTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getOtherOutOrder',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('stockoutapply_id', '出库申请单号'),
                    common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'performer_user_id', '审核人', 'staffInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'otherstockout_state', '出库状态', 'storeTypeInfo'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 stock">出库</a>';
                    }, tableEvents)
                ],
                idField: 'otherstockout_id',
                uniqueId: 'otherstockout_id',
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
            common.changeTableClass($otherOutTable)
        },
      initProductOutTable:function(){
        let _self = this;
        let $productOutTable = $('#productOutTable');
        window.tableEvents = {
          'click .stock': (e, value, row, index) => {
            this.$router.push({path: '/nca/inventorymanage/NCAProductOutItemsControl', query: row});
          }
        };

        $productOutTable.bootstrapTable({
          method: 'POST',
          url: apiUrl + 'getProductOutOrder',
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
            common.BTRowFormatEdSelect2Disabled(this, 'stock_out_state', '出库状态', 'storeTypeInfo'),
            common.actFormatter('act', () => {
              return '<a class="btn btn-info btn-xs m-r-5 stock">出库</a>';
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
        common.changeTableClass($productOutTable)
      }
    },
    mounted: function() {
        this.masterTable[0] = $('#saleOutTable');
        this.initPage();
    }
}
</script>
<style scoped>
    .select-width {
        width: 100px;
    }
</style>
