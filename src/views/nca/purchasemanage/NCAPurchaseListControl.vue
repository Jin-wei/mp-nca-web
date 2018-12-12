<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li class="active">物料采购列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">物料采购列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" v-model="search_text" placeholder="搜索采购订单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="purchaseTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/purchasemanage/NCAPurchaseListControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                apiName: common.getApiName(apiUrl),
                search_text: null,
                masterTable: null
            }
        },
        name: 'NCAPurchaseControl',
        components: {

        },
        mounted: function() {
            this.masterTable = $('#purchaseTable');
            this.initData();
        },
        methods: {
            nameFormatter: function (value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer" title="查看">',
                    value,
                    '</a>'
                ].join('')
            },
            queryParamsPurchase: function (params) {
                params.search_text = this.search_text;
                params.supplier_id = this.pagePara.thirdUser.supplier_id;
                return JSON.stringify(params)
            },
            queryConfirm: function(event) {
                this.masterTable.bootstrapTable("refresh")
            },
            initPurchaseTable: function () {
                window.tableEvents = {
                    'click .show_detail': (e, value, row, index) => {
                        this.$router.push({
                            path: '/nca/purchasemanage/NCAPurchaseDetailControl',
                            query: {
                                purchaseorder_id: row.purchaseorder_id
                            }
                        })
                    }
                };
                this.masterTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getPurchaseOrder',
                    queryParams: this.queryParamsPurchase,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('purchaseorder_id', '采购单号', this.nameFormatter, tableEvents),
                        common.BTRowFormat('purchase_domain', '采购机构'),
                        common.BTRowFormat('order_domain', '销售机构'),
                        common.BTRowFormat('supplier_name', '供应商名称'),
                        common.BTRowFormat('created_at', '创建日期'),
                    ],
                    idField: 'purchaseorder_id',
                    uniqueId: 'purchaseorder_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass(this.masterTable)
            },
            initData: async function () {
                let response = await this.$http.post(apiUrl + 'getSupplerUser', {});
                this.pagePara = response.data.info;

                this.initPurchaseTable();
                common.reSizeCall();
            }
        }
    }
</script>
<style scoped>
</style>
