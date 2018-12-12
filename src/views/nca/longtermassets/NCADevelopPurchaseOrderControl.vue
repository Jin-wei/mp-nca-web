<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">研发资产管理</a></li>
            <li class="active">材料采购单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料采购单列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索采购单编号，项目编号，项目名称" style="width: 20em;">
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
                state: 1,
            }
        },
        name: 'NCADevelopPurchaseOrderControl',
        mounted: function() {
            let _self = this;
            let $purchaseDevelopTable = $('#purchaseDevelopTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }

            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCADevelopPurchaseOrderDetailControl',
                        query: {
                            developpurchaseorder_id: row.developpurchaseorder_id
                        }
                    })
                }
            };

            function queryDevelopParams(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initDevelopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('purchaseorder_code', '采购单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('develop_code', '项目编号'),
                    common.BTRowFormatAlignLeft('develop_name', '项目名称'),
                    common.BTRowFormatAlignLeft('supplier_name', '供应商名称'),
                    common.BTRowFormatAlignLeft('creatorName', '采购人'),
                    common.BTRowFormat('created_at', '创建时间'),
                ];
                $purchaseDevelopTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getDevelopPurchaseOrder',
                    queryParams: queryDevelopParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'developpurchaseorder_id',
                    uniqueId: 'developpurchaseorder_id',
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
    .span1{
        font-size: 1.2em;
    }
    .padding-left{
        padding-left: 2em;
    }
    .select-width {
        width: 180px;
    }
</style>
