<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">研发资产管理</a></li>
            <li class="active">材料采购单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料采购单</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索物料名称">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="purchaseDevelopTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCADevelopPurchaseOrderControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                state: '',
            }
        },
        name: 'NCADevelopPurchaseOrderDetailControl',
        mounted: function() {
            let _self = this;
            let $purchaseDevelopTable = $('#purchaseDevelopTable');
            _self.developpurchaseorder_id = _self.$route.query.developpurchaseorder_id;

            function queryDevelopParams(params) {
                params.search_text=$('#search_text').val();
                params.developpurchaseorder_id=_self.developpurchaseorder_id;
                return JSON.stringify(params)
            }
            function initDevelopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('purchaseorderdetail_name', '物料名称'),
                    common.BTRowFormat('purchaseorderdetail_format', '规格型号'),
                    common.BTRowFormat('purchaseorderdetail_unit', '计量单位'),
                    common.BTRowFormatAlignRight('purchaseorderdetail_number', '采购数量'),
                    common.BTRowFormatWithFormatterAlignRight('purchaseorderdetail_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('purchase_total_money', '金额',common.priceFormat)
                ];
                $purchaseDevelopTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getDevelopPurchaseOrderDetail',
                    queryParams: queryDevelopParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'developpurchaseorderdetail_id',
                    uniqueId: 'developpurchaseorderdetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($purchaseDevelopTable)
            }

            $(function() {
                initDevelopTable();
                common.reSizeCall();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#purchaseDevelopTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
</style>
