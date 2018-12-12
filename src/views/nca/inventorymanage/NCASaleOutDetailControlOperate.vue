<template>
    <div>
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">销售出库管理</a></li>
            <li class="active">出库任务</li>
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
    const apiUrl = '/api/nca/inventorymanage/NCASaleOutControl?method='

    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                submitRow:[],
                oldRow: ''
            }
        },
        name: 'NCASaleOutDetailControlOperate',
        mounted: function() {
            let _self = this
            let $table = $('#table')

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSaleOutOrderMaterielOperate',
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
                        common.BTRowFormatEdSelectAlignLeft(_self, 'warehouse_zone_id', '仓区名', 'warehouseZoneId'),
                        common.BTRowFormat('warehouse_zone_id', '仓区ID'),
                        common.BTRowFormatAlignRight('demand_amount', '需要出库总数量'),
                        common.BTRowFormatAlignRight('done_count', '已出库总数量'),
                        common.BTRowFormatEditableRight('stock_operate_amount', '本次出库数量',value => {
                            return value || 0;
                        })
                    ],
                    idField: 'alldemand_id',
                    uniqueId: 'alldemand_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: async function(field, row, oldValue, $el) {
                        _self.$http.post(apiUrl + 'checkOutNumber',row).then((response) => {
                        }, (response) => {
                            $table.bootstrapTable('updateByUniqueId', {
                                id: row.alldemand_id,
                                row: _self.oldRow
                            });
                            common.dealErrorCommon(_self, response)
                        });

                    }
                })
                common.changeTableClass($table)
            }

            function queryParams(params) {

                params.search_text = $('#search_text').val();
                params.order_id =_self.$route.query.order_id;
                params.warehouse_id = _self.$route.query.warehouse_id;
                params.warehouse_zone_id = _self.$route.query.warehouse_zone_id;
                params.materiel =_self.$route.query.materiel;
                return JSON.stringify(params)
            }

            async function initData() {
                await _self.getPagePara()
                common.initSelect2($('#materiel_unit'), _self.pagePara.unitInfo);
                common.initSelect2($('#warehouse_zone_id'), _self.pagePara.warehouseZoneId);
                initTable();
                common.reSizeCall();
            }
            initData()
        },
        methods: {
            getPagePara: async function(event) {
                let _self = this;
                try {
                    let response = await _self.$http.post(apiUrl + 'initSaleOut',{warehouse_id:_self.$route.query.warehouse_id});
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
                let params={};
                params.materiel= $('table').bootstrapTable('getData');
                console.log(params);
                _self.$http.post(apiUrl + 'StockSaleOut',params).then((response) => {
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