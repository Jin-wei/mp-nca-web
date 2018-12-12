<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">闲置库存申请单</a></li>
            <li class="active">闲置库存申请单详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">闲置库存申请单详情</h4>
                    </div>
                    <div class="panel-body">
                        <div id="toolbar" class="pull-left">
                            <div class="form-inline" role="form">
                                <div class="form-group" style="margin-bottom: 10px">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text" placeholder="搜索物料编码,物料名称" style="width: 230px;">
                                    </div>
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                        <table id="idleItemTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCAIdleApplyControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{},
                idleapply_id: null,
                show_return: 0
            }
        },
        name: 'NCAIdleApplyDetailControl',
        mounted: function() {
            let _self = this;
            let $idleItemTable = $('#idleItemTable');
            _self.idleapply_id = _self.$route.query.idleapply_id

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                initIdleItemTable();
                common.reSizeCall();
            }
            function queryParams(params) {
                params.idleapply_id =_self.idleapply_id;
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initIdleItemTable() {
                window.tableEvents = {
                };
                let colums = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('idle_item_amount', '数量'),
                    common.BTRowFormat('order_id', '销售订单号'),
                    common.BTRowFormatAlignLeft('warehouse_name', '仓库'),
                    common.BTRowFormatAlignLeft('zone_name', '仓区')
                ];
                $idleItemTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchItem',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: colums,
                    idField: 'idleapplyitem_id',
                    uniqueId: 'idleapplyitem_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'idleapplyitem_id', $idleItemTable)
                    }
                });
                common.changeTableClass($idleItemTable)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#idleItemTable').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>