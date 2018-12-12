<template>
    <div>
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">供应商维护</a></li>
            <li class="active">供应商物料列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">供应商物料列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索编号、名称、地址等"
                                       style="width: 200px;">
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
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索物料编码、名称、型号" id="search_textM"
                                   style="width: 280px">
                        </div>
                        <div class="form-group p_a2">
                            <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>

                        <form @submit.prevent="addDm" id="formA">
                            <div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label">单次最低送货数量:</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.suppliermateriel_mincount"
                                               data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label">不含税单价:</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.suppliermateriel_purchaseprice"
                                               data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label">最短送货时间(天):</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.suppliermateriel_deliveryday"
                                               data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">税率:</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.suppliermateriel_tax"
                                               data-parsley-required="true">
                                        <span class="p_a">%</span>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">单价有效日期:</label>
                                    <div class="col-sm-7">
                                        <input type="text" class="form-control" id="suppliermateriel_effectivedata"
                                               data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">单价失效日期:</label>
                                    <div class="col-sm-7">
                                        <input type="text" class="form-control" id="suppliermateriel_expirydate"
                                               data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">价格生效依据:</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="suppliermateriel_priceeffective"
                                                data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                            </div>
                            <div>
                                <table id="tableM"></table>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" class="btn btn-info">确认</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="ShowModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">查看物料</h4>
                    </div>
                    <form @submit.prevent="showDM" id="formB">
                        <div class="modal-body row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label">最短送货时间(天):</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.suppliermateriel_deliveryday"
                                               id="suppliermateriel_deliveryday" data-parsley-required="true" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">单价有效日期:</label>
                                    <div class="col-sm-7">
                                        <input type="text" class="form-control" id="show_suppliermateriel_effectivedata"
                                               data-parsley-required="true"
                                               v-model="workRow.suppliermateriel_effectivedatam" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">单价失效日期:</label>
                                    <div class="col-sm-7">
                                        <input type="text" class="form-control" id="show_suppliermateriel_expirydate"
                                               data-parsley-required="true"
                                               v-model="workRow.suppliermateriel_effectivedatam" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6 p_r">
                                    <label class="col-sm-5 control-label">价格生效依据:</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="show_suppliermateriel_priceeffective"
                                                data-parsley-required="true" data-parsley-group="block-1"
                                                disabled></select>
                                    </div>
                                </div>
                         </div>
                    </form>
                </div>
            </div>
        </div>


    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/baseconfig/NCASupplierMaterielControl?method='

    function queryParams(params) {
        params.search_text = $('#search_text').val();
        params.supplier_id = common.getUrlParams('supplierId')
        return JSON.stringify(params)
    }

    export default {
        data: function () {
            return {
                pagePara: '',
                workRow: {},
                oldRow: '',
                searchedRow: [],
                saveId: ''
            }
        },
        name: 'supplierOfferControl',
        mounted: function () {
            let _self = this
            _self.supplier_id = common.getUrlParams('supplierId')
            let $table = $('#table')
            let $tableM = $('#tableM')

            function timeFormatter(value, row) {
                if (!value) return '';
                let date = value;
                date = date.split('T');
                let time = date[1].slice(0, 8);
                return date[0]
            }

            function actFormatter(value, row) {
                return [
                    '<a class="tableEdit btn btn-primary btn-xs m-r-5 delete_template btn-info-delete" title="查看">查看</a>',
                    '<a class="tableDelete btn btn-primary btn-xs m-r-5 delete_template btn-info-delete" title="删除">删除</a>'
                ].join('')
            }

            let rowDelete = function (_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function () {
                    _self.$http.post(apiUrl + 'delete', row).then((response) => {
                        $('#table').bootstrapTable('remove', {
                            field: key,
                            values: [row[key]]
                        });
                        common.dealSuccessCommon('删除成功');
                        console.log('delete success');
                    }, (response) => {
                        console.log('delete error');
                        common.dealErrorCommon(_self, response);
                    });
                });
            };

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormatEditablePop('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('suppliermateriel_mincount', '单次最低送货数量'),
                        common.BTRowFormatWithFormatterAlignRight('suppliermateriel_purchaseprice', '不含税单价', common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('suppliermateriel_purchasepricetax', '含税单价', common.priceFormat),
                        // common.BTRowFormatAlignRight('suppliermateriel_deliveryday', '最短送货时间(天)'),
                        common.BTRowFormatAlignRight('suppliermateriel_tax', '税率'),
                        // common.BTRowFormatWithFormatter('suppliermateriel_effectivedata', '单价有效日期', timeFormatter),
                        // common.BTRowFormatWithFormatter('suppliermateriel_expirydate', '单价失效日期', timeFormatter),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'suppliermateriel_priceeffective', '价格生效依据', 'priceEffective'),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ],
                    idField: 'suppliermateriel_id',
                    uniqueId: 'suppliermateriel_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'suppliermateriel_id', $table)
                    }
                })
                common.changeTableClass($table)
            }

            window.tableEvents = {
                'click .tableDelete': function (e, value, row, index) {
                    rowDelete(_self, '删除', apiUrl, row, 'suppliermateriel_id');
                },
                'click .tableEdit': function (e, value, row, index) {
                    $('#ShowModal').modal('show');
                    _self.saveId = row.suppliermateriel_id;
                    _self.workRow = $.extend(true, {}, row)
                    _self.workRow.suppliermateriel_effectivedatam =_self.workRow.suppliermateriel_effectivedata.split("T")[0];
                    _self.workRow.suppliermateriel_expirydatem =_self.workRow.suppliermateriel_expirydate.split("T")[0];
                    $('#suppliermateriel_deliveryday').val(_self.workRow.suppliermateriel_deliveryday).trigger('change');
                    $('#show_suppliermateriel_effectivedata').val(_self.workRow.suppliermateriel_effectivedatam ).trigger('change');
                    $('#show_suppliermateriel_expirydate').val(_self.workRow.suppliermateriel_expirydatem).trigger('change');
                    $('#show_suppliermateriel_priceeffective').val(_self.workRow.suppliermateriel_priceeffective).trigger('change');

                },
                'click .searchRow': function (e, value, row, index) {
                    _self.searchedRow = [];
                    _self.searchedRow.push(row)
                }
            };

            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#search_text_materiel').val();
                params.search_textM = $('#search_textM').val();
                return JSON.stringify(params)
            }

            function initMaterielTable() {
                $tableM.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_mat',
                    queryParams: queryParamsMateriel,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.actFormatter('act', actCheckBox, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo')
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'materiel_id', $tableM)
                    }
                })
                common.changeTableClass($tableM)
            }

            function actCheckBox(value, row) {
                return [
                    '<input type="radio" name="searchRow" class="searchRow">'
                ].join('')
            }

            async function initPage() {
                let response = await _self.$http.post(apiUrl + 'init', {})
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                common.initDatepicker($('#suppliermateriel_effectivedata'));
                common.initDatepicker($('#show_suppliermateriel_effectivedata'));
                common.initDatepicker($('#suppliermateriel_expirydate'));
                common.initDatepicker($('#show_suppliermateriel_expirydate'));
                common.initSelect2($('#suppliermateriel_priceeffective'), _self.pagePara.priceEffective);
                common.initSelect2($('#show_suppliermateriel_priceeffective'), _self.pagePara.priceEffective);
                initTable()
                initMaterielTable()
                common.reSizeCall()
                console.log('init success')
            }

            $('#formA').parsley()
            initPage()
        },
        methods: {
            search: function (event) {
                $('#table').bootstrapTable("refresh", queryParams)
            },
            search_mat: function (event) {
                $('#tableM').bootstrapTable("refresh", queryParamsMateriel)
            },
            queryConfirm: function (event) {
                $('#tableM').bootstrapTable("refresh")
            },
            addM: function (event) {
                let _self = this
                _self.workRow = {}
                $('#suppliermateriel_priceeffective').val(null).trigger('change');
                $('#formA').parsley().reset()
                $('#AddModal').modal('show')
            },
            showDM: function () {
                $('#ShowModal').modal('hide')
            },
            addDm: function () {
                let _self = this;
                if (_self.searchedRow.length == 0) {
                    common.dealPromptCommon('请选择物料')
                }
                if ($('#formA').parsley().isValid()) {
                    for (var i = 0; i < _self.searchedRow.length; i++) {
                        _self.workRow.materiel_id = _self.searchedRow[i].materiel_id
                        _self.workRow.supplier_id = _self.supplier_id;
                        _self.workRow.suppliermateriel_effectivedata = $('#suppliermateriel_effectivedata').val();
                        _self.workRow.suppliermateriel_expirydate = $('#suppliermateriel_expirydate').val();
                        _self.workRow.suppliermateriel_priceeffective = common.getSelect2Val('suppliermateriel_priceeffective');
                        _self.$http.post(apiUrl + 'SupplierMaterielControlSRV_add', _self.workRow).then((response) => {
                            $('#table').bootstrapTable("refresh", _self.queryParams)
                            $('#AddModal').modal('hide')
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }
                }


            }
        }
    }
</script>
<style scoped>
    .p_r {
        position: relative;
    }

    .p_a {
        position: absolute;
        top: 5px;
        right: 0px;
    }

    .m_t {
        margin-top: -24px;
    }

    .p_a2 {
        position: absolute;
        top: 15px;
        right: 242px;
    }

    .m_l {
        margin-left: 23px;
    }
</style>