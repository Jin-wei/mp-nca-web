<template>
    <div>
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">库存管理</a></li>
            <li class="active">其他出库申请单列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">其他出库申请单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索出库申请单号" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/inventorymanage/NCAStockOutApplyControl?method='

    function queryParams(params) {
        params.search_text = $('#search_text').val();
        return JSON.stringify(params)
    }

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                oldRow: ''
            }
        },
        name: 'NCAStockOutApplyControl',
        mounted: function() {
            let _self = this
            let $table = $('#table')

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
                        path: '/nca/inventorymanage/NCAStockOutApplyDetailControl',
                        query: {
                            stockoutapplyId: row.stockoutapply_id,
                        }
                    })
                }
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('stockoutapply_id', '出库申请单号', nameFormatter, tableEvents),
                        common.BTRowFormatEdSelect2Disabled(_self, 'stockoutapply_state', '状态', 'stockoutapplyState'),
                        common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'user_id', '申请人', 'staffInfo'),
                        common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'performer_user_id', '审核人', 'staffInfo'),
                        common.BTRowFormat('created_at', '提交时间'),
                        common.BTRowFormat('complete_date', '完成时间'),
                        common.BTRowFormatWithFormatterAlignLeft('stockoutapply_remark', '驳回说明',common.remarkFormatter),
                    ],
                    idField: 'stockoutapply_id',
                    uniqueId: 'stockoutapply_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'stockoutapply_id', $table)
                    }
                })
                common.changeTableClass($table)
            }

            async function initData() {
                await _self.getPagePara()
                initTable();
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
                $('#table').bootstrapTable("refresh")
            },
            addM: function(event) {
                let _self = this
                _self.workRow = {}
                common.dealConfrimCommon('确认新增出库申请单？', async () => {
                    _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                        $('#table').bootstrapTable("refresh")
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                });
            }
        }
    }
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:5px;
        right:0px;
    }
    .m_t{
        margin-top:-24px;
    }
    .p_a2{
        position:absolute;
        top:15px;
        right:242px;
    }
    .m_l{
        margin-left: 23px;
    }
</style>