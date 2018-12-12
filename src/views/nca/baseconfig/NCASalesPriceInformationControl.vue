<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">销售报价管理</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text2" placeholder="搜索产品编号、产品名称" style="width: 230px;">
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
                        <table id="salespriceTable"></table>
                    </div>

                    <div class="modal fade" id="salespriceModal">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">修改销售报价记录</h4>
                                </div>
                                <form @submit.prevent="submitForm" id="formPIC">
                                    <div class="modal-body row">
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">产品编号</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="materiel_code"  data-parsley-required="true" disabled>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">产品名称</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="materiel_name"  data-parsley-required="true" disabled>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">建议报价</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" id="PRICE"  data-parsley-required="true">
                                            </div>
                                        </div>
                                        <!--<div class="form-group col-sm-6">-->
                                            <!--<label class="col-sm-4 control-label">状态</label>-->
                                            <!--<div class="col-sm-8">-->
                                                <!--<select class="form-control select2" style="width:100%" id="produce_client_state" data-parsley-required="true" ></select>-->
                                            <!--</div>-->
                                        <!--</div>-->
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">有效日期</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="start_date" data-parsley-required="true">
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">失效日期</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="end_date" data-parsley-required="true">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="submit" class="btn btn-info">修改</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

export default {
    props: ['corporate', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            materiel_id: ''
        }
    },
    name: 'NCASalesPriceInformationControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        corporate: function() {
            let _self = this;
            let $salespriceTable = $('#salespriceTable');
            // common.initSelect2($('#produce_client_state'), this.pagePara.prodeuceInfo);
            common.startDatepicker($('#start_date'),$('#end_date'));
            common.endDatepicker($('#end_date'),$('#start_date'));
            // common.initDatepicker($('#start_date'));
            // common.initDatepicker($('#end_date'));

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 edit_detail">修改</a>'
                ].join('')

            }
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)
                    _self.materiel_id = row.materiel_id;
                    $('#materiel_code').val(row.materiel_code).trigger('change');
                    $('#materiel_name').val(row.materiel_name).trigger('change');
                    $('#PRICE').val(row.PRICE).trigger('change');
                    // $('#produce_client_state').val(row.produce_client_state).trigger('change');
                    $('#start_date').val(row.start_date).trigger('change');
                    $('#end_date').val(row.end_date).trigger('change');
                    $('#formPIC').parsley().reset();
                    $('#salespriceModal').modal('show');
                }
            };
            function queryParams(params) {
                params.corporateclients_id =_self.corporate.corporateclients_id;
                params.search_text2=$('#search_text2').val();
                params.producepricetemplate_id = common.getUrlParams('corporateclients_type');
                return JSON.stringify(params);
            }

            function initTable() {
                $salespriceTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_price',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '产品编号'),
                        common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormat('materiel_unit', '单位'),
                        common.BTRowFormatWithFormatterAlignRight('PRICE', '建议报价',common.priceFormat),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'produce_client_state', '状态', 'prodeuceInfo'),
                        common.BTRowFormat('start_date', '有效开始日期'),
                        common.BTRowFormat('end_date', '有效结束日期'),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($salespriceTable)
            }
            $(function() {
                initTable();
            })
        }
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#salespriceTable').bootstrapTable("refresh")
        },
        submitForm: async function(event) {
            let _self = this;
            let formPIC = $('#formPIC')
            if (formPIC.parsley().isValid()) {
                let param = {
                    corporateclients_id: _self.corporate.corporateclients_id,
                    materiel_id: _self.materiel_id,
                    suggest_price: $("#PRICE").val(),
                    start_date: $("#start_date").val(),
                    end_date: $("#end_date").val(),
                    // produce_client_state: common.getSelect2Val('produce_client_state')
                }

                _self.$http.post(apiUrl + 'add_p', param).then((response)=>{
                    let retData = response.data.info;
                    $('#salespriceTable').bootstrapTable("refresh");
                    $('#salespriceModal').modal('hide');
                    _self.workRow = {};
                    common.dealSuccessCommon('已提交');
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
            }
        }
    }
}
</script>
<style scoped>
</style>
