<template>
<div>
    <div v-show="!checkState('SIGNED')">
        等待内部审核完成
    </div>
    <div v-show="checkLastState('SIGNED')" class="panel panel-inverse">
        <div class="panel-heading">
            <h4 class="panel-title">合同信息</h4>
        </div>
        <form @submit.prevent="saveContract" id="formOrder1">
            <div class="panel-body">
                <div class="row" style="padding-left: 10px;padding-right: 10px" v-show="rules.length > 0">
                    <table id="ruleTable"></table>
                </div>
                <div style="margin-top: 20px">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">合同金额</span>
                                <div>
                                    <input class="form-control" id="order_price_contract" readonly>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon"><i class="symbol">*</i>预计开工日期</span>
                                <input class="form-control" id="start_date_contract" placeholder="选择开工日期"
                                       v-bind:disabled="checkState('REVIEWING')" autocomplete="off">
                            </div>
                        </div>
                    </div>
                    <div class="row">
                        <div class="form-group col-sm-12">
                            <div class="input-group">
                                <span class="input-group-addon">备注</span>
                                <div>
                                <textarea rows="5" class="form-control" style="resize:none;" maxlength="200" v-model="order.contract_remark"
                                          v-bind:disabled="checkState('REVIEWING')"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal-footer" style="border-top:none">
                <button type="submit" class="btn btn-info">提交合同
                    </button>
            </div>
        </form>
    </div>
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <h4 class="panel-title">订单主要信息</h4>
        </div>
        <div class="panel-body row">
            <table class="table-border" style="margin-left: 10px;">
                <thead>
                    <tr>
                        <th>订单号</th>
                        <th>客户姓名</th>
                        <th>客户手机</th>
                        <th>装修地址</th>
                        <th>户型</th>
                        <th>面积（平米）</th>
                        <th>工程类型</th>
                        <th>设计师</th>
                        <th>创建时间</th>
                    </tr>
                </thead>
                <tr>
                    <td>{{order.order_id}}</td>
                    <td>{{order.name}}</td>
                    <td>{{order.phone}}</td>
                    <td>{{order.order_address}}</td>
                    <td>{{roomTypeInfo.roomtype_spec_name}}</td>
                    <td>{{orderInfo.order_house_area}}</td>
                    <td>{{orderInfo.orderTypeName}}</td>
                    <td>{{orderInfo.disignerName}}</td>
                    <td>{{order.created_at|date_format}}</td>
                </tr>
            </table>
        </div>
    </div>
    <div v-show="checkState('SIGNED')" class="panel panel-inverse">
        <div class="panel-heading">
            <h4 class="panel-title">合同信息</h4>
        </div>
        <div class="panel-body row">
            <table class="table-border" style="margin-left: 10px">
                <thead>
                    <tr>
                        <th>合同自编号</th>
                        <th>合同金额</th>
                        <th v-for="r in rules">{{r.receivables_rule_name}}</th>
                        <th>预计开工日期</th>
                        <th>备注</th>
                        <th>经办人</th>
                        <th>创建日期</th>
                    </tr>
                </thead>
                <tr>
                    <td>{{orderInfo.order_id}}</td>
                    <td>{{orderInfo.final_offer}}</td>
                    <th v-for="r in rules">{{r.receivables_rule_rate*orderInfo.final_offer}}</th>
                    <td>{{orderInfo.break_date}}</td>
                    <td style="max-width:200px;">{{orderInfo.contract_remark}}</td>
                    <td>{{orderInfo.contract_operator_name}}</td>
                    <td>{{orderInfo.contract_date}}</td>
                </tr>
            </table>
        </div>
    </div>
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <div class="btn-group">
                    <label class="btn btn-info btn-xs fileupload-button">上传合同
                            <input id='contractUpload' type="file" name="file">
                        </label>
                </div>
            </div>
            <h4 class="panel-title">合同文档</h4>
        </div>
        <table id="fileTable1"></table>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';

//获取预付金额 >=65%, <95%
function validPrePayValue(offer, prePay) {
    let min = (offer * 0.65).toFixed(0)
    let max = (offer * 0.95).toFixed(0)
    if (!prePay || parseFloat(prePay) < parseFloat(min) || parseFloat(prePay) >= parseFloat(max)) {
        return false
    } else {
        return true
    }
}

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

export default {
    props: ['order', 'pPagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            pagePara: {},
            orderInfo: {},
            range: '[]',
            roomTypeInfo: {},
            rules: []
        }
    },
    name: 'contractControl',
    filters: {
        date_format(value) {
            return new Date(value).toLocaleDateString();
        }
    },
    mounted: function() {

    },
    watch: {
        order: function() {
            let _self = this;
            let $fileTable = $('#fileTable1');
            let $receivablesTable = $('#receivablesTable')

            function initFileTable() {
                function fileActFormatter(value, row, index) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 fileDelete">删除</a>'
                    ].join('')
                }

                function urlFormatter(value, row) {
                    return [
                        '<a class="download_url">',
                        value,
                        '</a>'
                    ].join('')
                }

                window.contractEvents = {
                    'click .fileDelete': function(e, value, row, index) {
                        common.rowDeleteWithApi(_self, '文件删除', apiUrl + 'contract_delete', $fileTable, row, 'file_id', function() {})
                    },
                    'click .download_url': function(e, value, row, index) {
                        window.open(row.file_url)
                    }
                }

                $fileTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('file_name', '文档名称'),
                        common.BTRowFormatEditableLeft('file_content', '文档内容描述'),
                        common.BTRowFormatWithFormatter('file_url', '文件', common.fileFormatter),
                        common.BTRowFormat('created_time', '创建日期'),
                        common.BTRowFormatAlignLeft('file_creator', '创建人'),
                        common.actFormatter('act', fileActFormatter, contractEvents)
                    ],
                    idField: 'file_id',
                    uniqueId: 'file_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'contract_modify_f', row, 'file_id', $fileTable)
                    }
                })
                common.changeTableClass($fileTable)
            }

            function getContractData() {
                let params = {};
                params.order_id = _self.order.order_id;
                _self.$http.post(apiUrl + 'contract_search', params).then((response) => {
                    let retData = response.data.info
                    $fileTable.bootstrapTable('load', {
                        data: retData
                    })
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }

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
                        /*common.BTRowFormatWithFormatter('receivables_amount', '金额', receivableTypeFilter),*/
                        common.BTRowFormatWithFormatterAlignRight('receivables_amount', '金额',common.priceFormat),
                        common.BTRowFormatEdSelect2(_self, 'receivables_type', '类型', 'receivablesTypeInfo'),
                        common.BTRowFormatEdSelect2(_self, 'receivables_pay_type', '支付方式', 'payTypeInfo'),
                        common.BTRowFormatEdSelect2(_self, 'receivables_operator_id', '收款人', 'opersInfo'),
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
                common.changeTableClass($fileTable)
            }

            function getRoomeType () {
                if (!_self.orderInfo.roomtype_id) {
                    return
                }
                let params={
                    roomtype_id:_self.orderInfo.roomtype_id
                }
                _self.$http.post(apiUrl + 'findRoomType', params).then((response) => {
                    _self.roomTypeInfo = response.data.info
                }, (response) => {
                    console.log('getRoomTypeByEstateId error')
                    common.dealErrorCommon(_self, response)
                })
            }

          function getRules () {
            _self.$http.post(apiUrl + 'search_rule', {}).then((response) => {
              _self.rules = response.data.info;
              $('#ruleTable').bootstrapTable('load', {
                data: _self.rules
              })
            }, (error) => {
              common.dealErrorCommon(_self, error)
            });

          }

          function rateFormatter(value, row, index) {
            return (value*100).toFixed(1) + '%';
          }
          function moneyFormatter(value, row, index) {
            return ((value*Number(_self.orderInfo.final_offer)).toFixed(2));

          }
          function initRuleTable() {
            $('#ruleTable').bootstrapTable({
              columns: [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('receivables_rule_name', '收款项名称'),
                common.BTRowFormatWithFormatterAlignRight('receivables_rule_rate', '收款比例',rateFormatter),
                common.BTRowFormatWithFormatterAlignRight('receivables_rule_rate', '金额',moneyFormatter)
              ],
              idField: 'receivablesrule_id',
              uniqueId: 'receivablesrule_id',
              striped: true,
              clickToSelect: true,
              locale: 'zh-CN'
            });
            common.changeTableClass($('#ruleTable'))
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
                    if (_self.order.break_date) {
                        _self.order.break_date = new Date(_self.order.break_date).toLocaleDateString()
                    }
                    $('#start_date_contract').val(_self.order.break_date)
                    _self.orderInfo = retData.orderInfo[0];


                    if (_self.pPagePara.projectTypeInfo) {
                        for (let i = 0; i < _self.pPagePara.projectTypeInfo.length; i++) {
                            if (_self.pPagePara.projectTypeInfo[i].id === _self.order.project_type) {
                                _self.orderInfo.orderTypeName = _self.pPagePara.projectTypeInfo[i].text;
                                break;
                            }
                        }
                    }

                    if (_self.pPagePara.employeeInfo) {
                        for (let i = 0; i < _self.pPagePara.employeeInfo.length; i++) {
                            if (_self.pPagePara.employeeInfo[i].id === _self.order.designer_id) {
                                _self.orderInfo.disignerName = _self.pPagePara.employeeInfo[i].text;
                            }
                        }
                    }

                    getRoomeType()

                    $("#order_price_contract").val((_self.orderInfo.final_offer).toFixed(2))
                    initFileTable();
                    initReceivablesTable()
                    getContractData();
                    getReceivablesData(_self)
                    $('#form').parsley();
                    initRuleTable();
                    getRules();
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
                    $fileTable.bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                }, (response) => {
                    console.log('init error')
                    common.dealErrorCommon(_self, response)
                })
            })
        }
    },
    methods: {
        saveContract: async function(event) {
            let _self = this;
            try {
                if (!$('#start_date_contract').val()) {
                    return common.dealWarningCommon('请选择开工日期')
                }
                _self.order.order_house_type = common.getSelect2Val('order_house_type');
                var params = {}
                params.order_id = _self.order.order_id;
                params.contract_remark = _self.order.contract_remark;
                params.break_date = $('#start_date_contract').val();
                params.order_deposit = 0;
                params.progress_payment = 0;
                params.final_payment = 0;
                params.user_id = common.getStoreData('userinfo').user_id;

                let response = await this.$http.post(apiUrl + 'contract_modify', params);
                let retData = response.data.info;
                _self.$parent.refreshOrder();
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }

        },
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        checkLastState: function(state, event) {
            let _self = this
            if (_self.order.order_state) {
                if (state == _self.order.order_state) return true
            }
            return false
        },
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
.table-border tr th {
    border: solid 1px grey;
    padding: 5px;
    text-align: center;
}

.table-border tr td {
    border: solid 1px grey;
    padding: 10px;
    word-wrap: break-word;
    word-break: break-all;
}

.symbol {
    color: red;
    position: absolute;
    top: 12px;
    left: -10px;
}
</style>
