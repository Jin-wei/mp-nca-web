<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">销售价格模板</a></li>
        <li class="active">销售价格详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn" style="position:relative">
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">销售价格详情</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_APDC" placeholder="搜索产品编号、产品名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <table id="productSalesPriceDetailTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddPSPDModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">编辑</h4>
                </div>
                <form @submit.prevent="submitForm" id="formDMDC">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>产品编号</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="workRow.materiel_code" data-parsley-required="true" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>产品名称</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="workRow.materiel_name" data-parsley-required="true" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>规格型号</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="workRow.materiel_format" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>计量单位</label>
                                <div class="col-sm-7">
                                    <select class="form-control select2" multiple id="materiel_unit" disabled> </select>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>建议报价</label>
                                <div class="col-sm-7">
                                    <input class="form-control" v-model="workRow.suggest_price"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>有效日期</label>
                                <div class="col-sm-7">
                                    <input type="text" class="form-control" id="start_date" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>失效日期</label>
                                <div class="col-sm-7">
                                    <input type="text" class="form-control" id="end_date" data-parsley-required="true">
                                </div>
                            </div>
                            <!--<div class="form-group col-sm-6">-->
                                <!--<label class="col-sm-4 control-label"><span class="table-required">*</span>状态</label>-->
                                <!--<div class="col-sm-7">-->
                                    <!--<select class="form-control select2" id="price_state" data-parsley-required="true"></select>-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">编辑</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAProductSalesPriceControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara:{},
            producepricetemplate_id:'',
            apply:{}
        }
    },
    name: 'NCAProductSalesPriceDetailControl',
    mounted: function() {
        let _self = this;
        let $productSalesPriceDetailTable = $('#productSalesPriceDetailTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            // common.initSelect2($('#price_state'), _self.pagePara.stateInfo);
            common.initSelect2($('#materiel_unit'), _self.pagePara.unitInfo);
            common.startDatepicker($('#start_date'),$('#end_date'));
            common.endDatepicker($('#end_date'),$('#start_date'));
            // common.initDatepicker($('#start_date'));
            // common.initDatepicker($('#end_date'));

            initAllDemandTable();
            common.reSizeCall();
            $('#formAPD').parsley()
        }

        function queryParams(params) {
            params.search_text=$('#search_text_APDC').val();
            params.producepricetemplate_id=common.getUrlParams('producepricetemplate_id');
            return JSON.stringify(params)
        }
        function ProductFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑</a>'
            ].join('')
        }

        function initAllDemandTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)

                    // $('#price_state').val(_self.workRow.price_state).trigger('change');
                    $("#start_date").val(_self.workRow.start_date).trigger('change');
                    $("#end_date").val(_self.workRow.end_date).trigger('change');
                    $("#materiel_unit").val(_self.workRow.materiel_unit).trigger('change');
                    $('#formDMDC').parsley().reset();
                    $('#AddPSPDModal').modal('show')
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('materiel_code', '产品编号'),
                common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                common.BTRowFormat('materiel_format', '规格型号'),
                common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '计量单位', 'unitInfo'),
                common.BTRowFormatWithFormatterAlignRight('suggest_price', '建议报价',common.priceFormat),
                common.BTRowFormat('start_date', '有效开始日期'),
                common.BTRowFormat('end_date', '有效结束日期'),
                // common.BTRowFormatEdSelect2Disabled(_self, 'price_state', '状态', 'stateInfo'),
                common.BTRowFormatWithFE('', '操作', ProductFormatter, tableEvents)
            ];

            $productSalesPriceDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'producepricetemplatedetail_id',
                uniqueId: 'producepricetemplatedetail_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    // common.rowModifyWithT(_self, apiUrl + 'modify_pd', row, 'producepricetemplatedetail_id', $productSalesPriceDetailTable)
                }
            });
            common.changeTableClass($productSalesPriceDetailTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#productSalesPriceDetailTable').bootstrapTable("refresh")
        },
        submitForm: async function(event) {
            if ($('#formDMDC').parsley().isValid()) {
                let _self = this;
                try {
                    _self.workRow.producepricetemplate_id=common.getUrlParams('producepricetemplate_id');
                    // _self.workRow.price_state = common.getSelect2Val('price_state');
                    _self.workRow.start_date = $("#start_date").val();
                    _self.workRow.end_date = $("#end_date").val();

                    common.rowModifyWithT(_self, apiUrl + 'modify_d', _self.workRow, 'producepricetemplatedetail_id', $('#assetsAcceptanceOrderTable'));
                    console.log('success');
                    window.setTimeout(function () {
                    $('#productSalesPriceDetailTable').bootstrapTable("refresh");
                    },500);
                    _self.workRow = {};
                    $('#AddPSPDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
}
</script>
<style scoped>
</style>
