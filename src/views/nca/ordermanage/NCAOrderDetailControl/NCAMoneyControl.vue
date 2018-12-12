<template>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <div class="btn-group">
                        <button class="btn btn-info btn-xs" @click="addReceivablesClick">新增收款记录</button>
                    </div>
                </div>
                <h4 class="panel-title">收款记录</h4>
            </div>
            <table id="receivablesTable"></table>
        </div>

        <div class="modal fade" id="AddReceivables">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增收款记录</h4>
                    </div>
                    <form @submit.prevent="addReceivablesRequest" id="form">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6 p_r">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>金额</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.receivables_amount" data-parsley-type="number" data-parsley-required="true" data-parsley-range="[1, 9999999]">
                                </div>
                            </div>
                            <div class="form-group col-sm-6 p_r">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>收款人</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" style="width:100%" id="receivables_operator_id" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6 p_r">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>类型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" style="width:100%" id="receivables_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6 p_r">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>支付方式</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" style="width:100%" id="receivables_pay_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

    </div>
</template>

<script>

    const common = require('commonFunc');
    const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';
    //获取收款记录
    function getReceivablesData(_self) {
        let params = {};
        params.order_id = _self.order.order_id;
        _self.$http.post(apiUrl + 'contract_receivables_search', params).then((response) => {
            let retData = response.data.info
            $('#receivablesTable').bootstrapTable('load', {
                data: retData
            })
        }, (response) => {
            common.dealErrorCommon(_self, response)
        })
    }

    export default{
        props: ['order', 'pPagePara'],
        data: function() {
            return {
                workRow: {},
                oldRow: '',
                pagePara: {},
                orderInfo: {},
                range: '[]'
            }
        },
        watch: {
            order: function () {
                let _self = this;

                let $receivablesTable = $('#receivablesTable');

                function receivableTypeFilter(value, row) {
                    if (row.receivables_type === '6') {
                        return '-' + value;
                    } else {
                        return value;
                    }
                }

                function initReceivablesTable() {
//收款记录
                    function deleteFormatter(value, row, index) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 receivablesDelete">删除</a>'
                        ].join('')
                    }

                    window.contractEvents = {
                        'click .receivablesDelete': function(e, value, row, index) {
                            common.rowDeleteWithApi(_self, '删除收款记录', apiUrl + 'contract_receivables_delete', $receivablesTable, row, 'receivables_id', function() {})
                        }
                    }
                    $receivablesTable.bootstrapTable({
                        columns: [
                            common.BTRowFormatWithIndex('NO.'),
                            common.BTRowFormatWithFormatterAlignRight('receivables_amount', '金额',common.priceFormat),
                            common.BTRowFormatEdSelect(_self, 'receivables_pay_type', '支付方式', 'payTypeInfo'),
                            common.BTRowFormatEdSelectAlignLeft(_self, 'receivables_operator_id', '收款人', 'opersInfo'),
                            common.BTRowFormatEdSelect2(_self, 'receivables_type', '类型', 'receivablesTypeInfo'),
                            common.BTRowFormat('create_date', '创建日期')
                        ],
                        idField: 'receivables_id',
                        uniqueId: 'receivables_id',
                        clickToSelect: true,
                        locale: 'zh-CN',
                        onEditableShown: function(field, row, $el, editable) {
                            _self.oldRow = $.extend(true, {}, row)
                        },
                        onEditableSave: function(field, row, oldValue, $el) {
                            common.rowModifyWithT(_self, apiUrl + 'contract_receivables_modify', row, 'receivables_id', $receivablesTable)
                        }
                    })

                }

                function initPage() {
                    common.initDatepickerStart($('#start_date_contract'));

                    _self.$http.post(apiUrl + 'contract_init', _self.order).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara = JSON.parse(JSON.stringify(retData))

                        common.initSelect2($('#pay_kind_contract'), retData.payTypeInfo);
                        common.initSelect2($('#receivables_pay_type'), retData.payTypeInfo);
                        common.initSelect2($('#receivables_type'), retData.receivablesTypeInfo);
                        common.initSelect2($('#receivables_operator_id'), retData.opersInfo);

                        $('#pay_kind_contract').val(_self.order.pay_kind).trigger('change');
                        $('#start_date_contract').val(_self.order.break_date)
                        _self.orderInfo = retData.orderInfo[0];

//                        if (_self.pPagePara.roomtype && _self.order.roomtype_id) {
//                            for (let i = 0; i < _self.pPagePara.roomtype.length; i++) {
//                                if (_self.pPagePara.roomtype[i].value === _self.order.roomtype_id) {
//                                    _self.orderInfo.houseTypeName = _self.pPagePara.roomtype[i].text;
//                                }
//                            }
//                        }
//                        if (_self.pPagePara.orderTypeInfo) {
//                            for (let i = 0; i < _self.pPagePara.orderTypeInfo.length; i++) {
//                                if (_self.pPagePara.orderTypeInfo[i].value === _self.order.order_type) {
//                                    _self.orderInfo.orderTypeName = _self.pPagePara.orderTypeInfo[i].text;
//                                }
//                            }
//                        }
//                        if (_self.pPagePara.employeeInfo) {
//                            for (let i = 0; i < _self.pPagePara.employeeInfo.length; i++) {
//                                if (_self.pPagePara.employeeInfo[i].id === _self.order.designer_id) {
//                                    _self.orderInfo.disignerName = _self.pPagePara.employeeInfo[i].text;
//                                }
//                            }
//                        }

                        $("#order_price_contract").val(_self.orderInfo.final_offer)
                        initReceivablesTable()
                        getReceivablesData(_self)
                        $('#form').parsley();
                        console.log('init success');

                    }, (response) => {
// error callback
                        console.log('get data error');
                        common.dealErrorCommon(_self, response)
                    });
                }

                initPage();

                common.fileUpload(_self, $('#contractUpload'), apiUrl, function(fileInfo) {
                    _self.workRow = {}
                    _self.workRow.order_id = _self.order.order_id
                    _self.workRow.file_name = fileInfo.name
                    _self.workRow.file_url = fileInfo.url
                    _self.workRow.file_type = fileInfo.type
                    _self.workRow.file_visible = '1'
                    _self.workRow.user_id = common.getStoreData('userinfo').user_id;
                    _self.workRow.user_name = common.getStoreData('userinfo').name;
                    _self.$http.post(apiUrl + 'contract_add_f', _self.workRow).then((response) => {
                        let retData = response.data.info
//                        $fileTable.bootstrapTable('insertRow', {
//                            index: 0,
//                            row: retData
//                        })
                    }, (response) => {
                        console.log('init error')
                        common.dealErrorCommon(_self, response)
                    })
                })
            }
        },
        methods:{
            addReceivablesClick: function() {
                let _self = this
                _self.workRow = {}
                $('#receivables_type').val(null).trigger('change');
                $('#receivables_pay_type').val(null).trigger('change');
                $('#receivables_operator_id').val(null).trigger('change');
                $('#AddReceivables').modal('show');
            },
            addReceivablesRequest: function() {
                let _self = this;
                let $form = $('#form')
                if ($form.parsley().isValid()) {
                    try {
                        _self.workRow.order_id = _self.order.order_id
                        _self.workRow.receivables_type = common.getSelect2Val('receivables_type')
                        _self.workRow.receivables_pay_type = common.getSelect2Val('receivables_pay_type')
                        _self.workRow.receivables_operator_id = common.getSelect2Val('receivables_operator_id')
                        _self.$http.post(apiUrl + 'contract_receivables_add', _self.workRow).then((response) => {
                            let retData = response.data.info
                            getReceivablesData(_self)
                            $form.parsley().reset();
                            $('#AddReceivables').modal('hide');
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        });
                    } catch (error) {
                        common.dealErrorCommon(_self, error)
                    }
                }
            },
        }
    }





</script>

<style scoped>
    .p_r{
        padding-right: 25px;
        padding-left: 0px;
    }

</style>
