<template>
<div>
    <!-- end breadcrumb -->
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
                            <input class="form-control" id="search_textB" placeholder="搜索产品编号、产品名称" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search2"><i class="fa fa-search"></i></button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="table"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="Modify" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新建项目</h4>
                </div>
                <form @submit.prevent="submitModify" id="formProject">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">产品编号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="materiel_code" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">产品名称</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="materiel_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">建议报价</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="PRICE" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">有效日期:</label>
                                <div class="col-sm-8">
                                    <input type="text" class="form-control" id="start_date" data-parsley-required="true">
                                </div>
                            </div>
                            <!--<div class="form-group col-xs-6">-->
                                <!--<label class="col-sm-4 control-label">状态</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<select class="form-control select2" multiple id="produce_client_state" data-parsley-required="true"> </select>-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">失效日期:</label>
                                <div class="col-sm-8">
                                    <input type="text" class="form-control" id="end_date" data-parsley-required="true">
                                </div>
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
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCABusinessCustomerControl?method=';

export default {
    data: function() {
        return {
            pagePara: '',
            workRow: {},
            oldRow: '',
            bpr: {},
            businesscustomer_type: ''
        }
    },
    name: 'NCABusinessSalePriceControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')
        _self.workRow = _self.$route.query;

        function queryParams(params) {
            params.domain_id = _self.workRow.domain_id;
            params.search_text = $('#search_textB').val();
            params.producepricetemplate_id = common.getUrlParams('businesscustomer_type');
            return JSON.stringify(params)
        }
        // function timeFormatter(value, row) {
        //     if (!value) return '';
        //     let date = value;
        //     date = date.split('T');
        //     let time = date[1].slice(0, 8);
        //     return date[0]
        // }

        function actFormatter(value, row) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 edit_detail">修改</a>'
                ].join('')
        }

        window.tableEvents = {
            'click .edit_detail': function(e, value, row, index) {
                _self.bpr.materiel_id = row.materiel_id
                $('#materiel_code').val(row.materiel_code).trigger('change')
                $('#materiel_name').val(row.materiel_name).trigger('change')
                $('#PRICE').val(row.PRICE).trigger('change')
                // $('#produce_client_state').val(row.produce_client_state).trigger('change')
                $('#start_date').val(row.start_date).trigger('change')
                $('#end_date').val(row.end_date).trigger('change')
                $('#Modify').modal('show')
            }
        }

        function initTable() {
            $table.bootstrapTable({
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
                    common.BTRowFormat('materiel_code', '产品编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格编码'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatWithFormatterAlignRight('PRICE', '建议报价',common.priceFormat),
                    // common.BTRowFormatEdSelect2Disabled(_self, 'produce_client_state', '状态', 'prodeuceInfo'),
                    common.BTRowFormat('start_date', '有效开始日期'),
                    common.BTRowFormat('end_date', '有效结束日期'),
                    // common.BTRowFormatWithFormatter('start_date', '有效开始日期', timeFormatter),
                    // common.BTRowFormatWithFormatter('有效结束日期', '有效结束日期', timeFormatter),
                    common.actFormatter('act', actFormatter, tableEvents),
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            common.initSelect2($('#order_state'), _self.pagePara.orderStateSalesInfo)
            // common.initSelect2($('#produce_client_state'), _self.pagePara.prodeuceInfo)
            common.startDatepicker($('#start_date'),$('#end_date'));
            common.endDatepicker($('#end_date'),$('#start_date'));
            // common.initDatepicker($('#start_date'));
            // common.initDatepicker($('#end_date'));
            initTable();
            console.log('dasda')
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
        search2: function(event) {
            $('#table').bootstrapTable("refresh")
        },
        submitModify: function() {
            let _self = this
            if ($('#formProject').parsley().isValid()) {
                let param = {
                    materiel_id: _self.bpr.materiel_id,
                    suggest_price: $("#PRICE").val(),
                    start_date: $("#start_date").val(),
                    end_date: $("#end_date").val(),
                    client_domain_id: _self.$route.query.domain_id,
                    // produce_client_state: common.getSelect2Val('produce_client_state')
                }
                _self.$http.post(apiUrl + 'addbusinessprice', param).then((response) => {
                    $('#table').bootstrapTable("refresh");
                    common.dealSuccessCommon('编辑成功');
                    $('#formProject').parsley().reset();
                    $('#Modify').modal('hide');
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
                $('#Modify').modal('hide')
            }
        },
    }
}
</script>
<style scoped>
</style>