<template>
    <div>
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">其他出库管理</a></li>
            <li class="active">出库信息</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <span class="panel-title">出库信息</span>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索物料编码、物料名称" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">
                                    提交
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

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                searchedRow:[],
                oldRow: ''
            }
        },
        name: 'NCAOtherOutDetailControlApply',
        mounted: function() {
            let _self = this
            let $table = $('#table')

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchOtherApplyOtherOut',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelectAlignLeft(_self, 'warehouse_zone_id', '仓区名', 'wzI'),
                        common.BTRowFormat('warehouse_zone_id', '仓区ID'),
                        common.BTRowFormatAlignRight('stockotherapplyout_amount', '需要出库总数量'),
                        common.BTRowFormatAlignRight('already_amount', '已出库总数量'),
                        common.BTRowFormatEditableRight('waitoutapply_amount', '本次出库总数量')
                    ],
                    idField: 'stockotherapplyout_id',
                    uniqueId: 'stockotherapplyout_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modifyOtherApplyOtherOut', row, 'stockotherapplyout_id', $table)
                    }
                })
                common.changeTableClass($table)
            }

            function queryParams(params) {
                params.search_text = $('#search_text').val();
                params.stockoutapply_id = _self.$route.query.stockoutapply_id;
                return JSON.stringify(params)
            }

            async function initData() {
                await _self.getPagePara()
                common.initSelect2($('#materiel_unit'), _self.pagePara.unitInfo);
                common.initSelect2($('#warehouse_zone_id'), _self.pagePara.wzI);
                initTable();
                common.reSizeCall();
            }
            initData()
        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                _self.workRow = _self.$route.query;
                let params={
                    warehouse_id:_self.workRow.warehouse_id,
                    stockoutapply_id:_self.workRow.stockoutapply_id
                }
                try {
                    let response = await _self.$http.post(apiUrl + 'initOtherApplyOtherOut',params);
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            search: function(event) {
                $('#table').bootstrapTable("refresh")
            },
            addM:function(event){
                let _self = this;
                _self.workRow = _self.$route.query;
                let params={
                    stockoutapply_id:_self.workRow.stockoutapply_id
                }
                _self.$http.post(apiUrl + 'StockOtherOut',params).then((response) => {
                    $('#table').bootstrapTable("refresh");
                    initData();
                    common.dealSuccessCommon('提交成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });

            },
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