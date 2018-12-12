<template>
<div>
    <div v-show="!checkState('SIGNED')">
        订单签约后方可提交变更记录，签约前可到"物料管理"标签下修改物料单。
    </div>
    <div v-show="checkState('SIGNED')">
        <div class="panel panel-inverse">
            <div class="panel-body">
                <div class="row">
                    <div class="col-md-12">
                        <div class="form-inline" role="form" style="padding-left: 0px;">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" multiple id="change_search_text" placeholder="搜索物料编码、物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="change_type">
                                        <option value="">请选择变更类型</option>
                                        <option v-for="state in retData.changeTypeInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                                <button class="btn btn-info" v-on:click="showDueDate">采购</button>
                            </div>
                        </div>
                    </div>
                </div>
                <div >
                    <table id="changeTable"></table>
                </div>
            </div>
            <div class="modal fade" id="AddChangeModal">
                <div class="modal-dialog modal-center">
                    <div class="modal-content" style="width: 900px;">
                        <div class="modal-header">
                            <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                            <h4 class="modal-title">新增变更记录</h4>
                        </div>
                        <div class="modal-body">
                            <form @submit.prevent="addAct" id="formChange">
                                <div class="form-group col-xs-12" style="padding: 0px;margin-bottom: 0px">
                                    <div class="form-group col-sm-4" style="padding-left: 0px">
                                        <label class="col-sm-3 control-label"><span class="table-required">*</span>空间</label>
                                        <div class="col-sm-9">
                                            <select class="form-control select2" multiple id="room_id" data-parsley-required="true"> </select>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-5" style="padding-left: 0px">
                                        <label class="col-sm-4 control-label"><span class="table-required">*</span>变更类型</label>
                                        <div class="col-sm-8">
                                            <select class="form-control select2" multiple id="change_type_m" data-parsley-required="true"> </select>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-3" style="padding-right: 0px">
                                        <label class="col-sm-4 control-label"><span class="table-required">*</span>金额</label>
                                        <div class="col-sm-8" style="padding-right: 0px">
                                            <input class="form-control" id="change_price" data-parsley-maxlength="10" data-parsley-required="true" data-parsley-type="integer" data-parsley-range="[0, 999999999]" />
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-xs-10" style="padding-left: 0px">
                                    <input class="form-control" v-model="mateRow.matNameOrCodeOrFormat" id="changeMaterialSelect" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                                </div>
                                <div class="form-group col-xs-2" style="padding-left: 40px;padding-right: 0px">
                                    <button type="submit" class="btn btn-info">保存</button>
                                </div>
                            </form>
                            <table id="changeTableSearch"></table>
                        </div>
                    </div>
                </div>
            </div>
            <div class="modal fade" id="changeDueModal">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                            <h4 class="modal-title">预交货日期</h4>
                        </div>
                        <form @submit.prevent="placePurchaseOrder" id="formdue">
                            <div class="modal-body">
                                <div class="form-group">
                                    <label>预交货日期</label>
                                    <input class="form-control" id='change_pre_delivery_date' data-parsley-required="true">
                                </div>
                                <div class="form-group">
                                    <label><span class="table-required">*</span>送货地址</label>
                                    <input class="form-control" id='change_delivery_addr' data-parsley-required="true">
                                </div>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" class="btn btn-info">提交</button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="changeModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">变更审核意见</h4>
                </div>
                <div class="modal-body">
                    <div class="form-horizontal">
                        <div class="form-group">
                            <label class="control-label col-md-2">审核意见</label>
                            <div class="col-md-10">
                                <textarea class="form-control" v-model="check_message" rows="5"></textarea>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info-reject" v-on:click="CheckChange('1', $event)">不同意</button>
                    <button type="button" class="btn btn-info" v-on:click="CheckChange('2', $event)">同意</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="checkHistoryModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">审核信息</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-12">
                        <label class="col-sm-12" v-for="changeHistory in changeHistoryList">
                                <b style="margin-right: 10px;">{{changeHistory.check_state}}</b><span>&nbsp;{{changeHistory.checker_name}}&nbsp;</span>|<span>&nbsp;{{changeHistory.check_message}}&nbsp;</span>|<span>&nbsp;{{changeHistory.create_date}}&nbsp;</span>
                            </label>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="commitChangeModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">物料变更审核</h4>
                </div>
                <form @submit.prevent="submitChange" id="formSubmit">
                    <div class="modal-body row">
                        <div class="form-horizontal">
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label">审核人</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2" multiple id="task_performer_select" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <label class="control-label col-sm-2">任务描述</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" id="task_description" rows="5"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" >提交</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const orderDetailApiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';
let materialInfo = {};
////员工角色
//let staffType = 0;

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            templateId: '',
            templateName: '',
            searchedRow: [],
            purchaseSelects: [],
            mateRow: {},
            orderOD: {},
            listType: 1,
            check_message: '',
            changeHistoryList: {},
            retData: {},
            staffType: 0,//员工角色
        }
    },
    name: 'changeControl',
    mounted: function() {},
    watch: {
        pagePara: function() {
          let _self = this
          common.initSelect2($('#task_performer_select'), _self.pagePara.performers);
        },
        order: function() {
            let _self = this
            _self.templateId = _self.order.template_id
            _self.pagePara.roomsInfo = $.extend(true, [], _self.order.roomsInfo)

            let $table = $('#changeTable');
            let $tableSearch = $('#changeTableSearch');

            function queryParams(params) {
                if ($('#change_search_text').val()) {
                    params.search_text = $('#change_search_text').val();
                }
                if ($('#change_type').val()) {
                    params.change_type = $('#change_type').val();
                }
                params.order_id = common.getUrlParams("orderId");
                params.change_flag = 1;
                return JSON.stringify(params);
            }

            function initSearch(retData) {
                $('#change_type').select2({
                    placeholder: '请选择变更类型',
                    allowClear: true,
                    maximumSelectionLength: 1,
                    language: 'zh-CN',
                    tags: false,
                    width: 140,
                    data: retData.changeTypeInfo
                })
            }

            function actCheckBox(value, row) {
                return [
                    '<input type="radio" name="searchRow" class="searchRow">'
                ].join('')
            }

            function actPurchaseSelect(value, row) {
                if (row.change_state != 2 || row.change_type == 2) {
                    return [
                        '<input type="checkbox" class="purchaseSelect" disabled>'
                    ].join('')
                } else if (row.purchase_id) {
                    return [
                        '<input type="checkbox" class="purchaseSelect" disabled checked>'
                    ].join('')
                } else {
                    return [
                        '<input type="checkbox" class="purchaseSelect">'
                    ].join('')
                }
            }

            function operateFormatter(value, row, index) {
                console.log('staffType' + _self.staffType);
                if (row.change_state == 0 || row.change_state == 3) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 delete btn-info-delete" style="cursor: pointer">',
                        '删除</i>',
                        '</a>',
                        '<a class="btn btn-info btn-xs m-r-5 commitChange" style="cursor: pointer">',
                        '提交</i>',
                        '</a>'
                    ].join('')
                }
//                else if (row.change_state == 1) {
//                    //该订单的变更审核员有审核权限
//                    return [
//                        '<a class="checkChange" style="cursor: pointer">',
//                        '审核</i>',
//                        '</a>'
//                    ].join('')
//                }
            }

            function queryParamsMateriel(params) {
                //params.materiel_source = _self.materiel_source
                params.matNameOrCodeOrFormat = $('#changeMaterialSelect').val();
                return JSON.stringify(params)
            }

            window.tableEvents = {
                'click .delete': function(e, value, row, index) {
                    rowDelete(_self, '物料删除', orderDetailApiUrl, row, 'ordermateriel_id')
                },
                'click .purchaseSelect': function(e, value, row, index) {
                    if ($(this).attr("checked") == 'checked') {
                        _self.purchaseSelects.push(row)
                    } else {
                        _self.purchaseSelects.splice($.inArray(row, _self.purchaseSelects), 1);
                    }
                },
                'click .searchRow': function(e, value, row, index) {
                    _self.searchedRow = [];
                    _self.searchedRow.push(row)
                },
                'click .change_check_detail': function(e, value, row, index) {
                    _self.changeHistoryList = {};

                    var params = new Object();
                    params.ordermateriel_id = row.ordermateriel_id;
                    _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_searchChangeHistory', params).then((response) => {
                        _self.changeHistoryList = response.data.info;
                        console.log('search success')
                    }, (response) => {
                        console.log('search error')
                        common.dealErrorCommon(_self, response)
                    });
                    $('#checkHistoryModal').modal('show')
                },
                'click .commitChange': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    $('#formSubmit').parsley().reset()
                    $('#commitChangeModal').modal('show')
//                    commitChange(_self, '提交审核', orderDetailApiUrl, row, 'materiel_id')
                },
                'click .checkChange': function(e, value, row, index) {
                    let _self = this
                    _self.check_message = null
                    materialInfo = row;
                    $('#changeModal').modal('show')
                }
            };

            let rowDelete = function(_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function() {
                    _self.$http.post(apiUrl + 'MaterielCrmControlSRV_delete', row).then((response) => {
                        $('#changeTable').bootstrapTable('remove', {
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

            let commitChange = function(_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function() {
                    if (row.materiel_amount <= 0) {
                        return common.dealPromptCommon('请填写物料数量')
                    }
                    changeState(1, '变更审核已提交', row, key);
                });
            };

            function changeState(params, msg, row, key) {
                let oldRow = row;
                row.change_state = params;
                _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_modify', {
                    'old': oldRow,
                    'new': row
                }).then((response) => {

                    let updaterow = response.data.info;
                    $('#changeTable').bootstrapTable('updateByUniqueId', {
                        id: row[key],
                        row: updaterow
                    });
                    $('#changeTable').bootstrapTable('resetView');
                    common.dealSuccessCommon(msg);
                    console.log('modify success')
                }, (response) => {
                    console.log('modify error');
                    exports.dealErrorCommon(this, response)

                    $('#changeTable').bootstrapTable('updateByUniqueId', {
                        id: row[key],
                        row: _self.oldRow
                    })
                    $('#changeTable').bootstrapTable('resetView')

                })
            }

            function changeStateFormatter(value, row) {
                let changeStateId = '';
                let changeStateName = '';
                for (let i = 0; i < _self.pagePara.changeStateInfo.length; i++) {
                    if (_self.pagePara.changeStateInfo[i].id == value) {
                        changeStateName = _self.pagePara.changeStateInfo[i].text;
                        changeStateId = _self.pagePara.changeStateInfo[i].id
                    }
                }

                if (changeStateId == 0 || changeStateId == 1) {
                    return [
                        changeStateName
                    ].join('')
                } else {
                    return [
                        '<a class="change_check_detail" style="cursor: pointer;">',
                        changeStateName,
                        '</a>'
                    ].join('')
                }
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: orderDetailApiUrl + 'MaterielCrmControlSRV_search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.actFormatter('checkAct', actPurchaseSelect, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEditableRight('materiel_amount', '数量'),
                        common.BTRowFormatEditableRight('change_price', '金额',common.priceFormat),
                        // common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatEditableLeft('room_id', '房间'),
                        // common.BTRowFormatAlignLeft('materiel_batch', '批次'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'change_typeq', '变更类型', 'changeTypeInfo'),
                        common.BTRowFormatWithFE('change_state', '审核状态', changeStateFormatter, tableEvents),
                        common.BTRowFormatEdSelect2Disabled(_self, 'purchase_state', '采购状态', 'purchaseTypeInfo'),
                        common.BTRowFormat('purchase_id', '采购单号'),
                        common.actFormatter('deleteAct', operateFormatter, tableEvents)
                    ],
                    idField: 'ordermateriel_id',
                    uniqueId: 'ordermateriel_id',
                    toolbar: '#changeToolbar',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-name="room_id"]').each(function() {
                            let actrow = $('#changeTable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                            if (actrow && (actrow.change_state === '0')) {
                                $(this).editable({
                                    type: 'select2',
                                    emptytext: '无',
                                    select2: {
                                        data: _self.pagePara.roomsInfo,
                                        width: 200
                                    },
                                    display: function(value) {
                                        let showText = ''
                                        $(_self.pagePara.roomsInfo).each(function() {
                                            if (this.id === value) {
                                                showText = this.text
                                                return
                                            }
                                        })
                                        $(this).html(showText)
                                    }
                                })
                            } else {
                                $(this).editable({
                                    type: 'text',
                                    emptytext: '无',
                                    disabled: true,
                                    display: function(value) {
                                        let showText = ''
                                        $(_self.pagePara.roomsInfo).each(function() {
                                            if (this.id === value) {
                                                showText = this.text
                                                return
                                            }
                                        })
                                        $(this).html(showText)
                                    }
                                })
                            }
                        })
                        $('[data-name="materiel_amount"]').each(function() {
                            let actrow = $('#changeTable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                            if (actrow && (actrow.change_state == 2 || actrow.change_state == 1)) {
                                $(this).editable({
                                    type: 'text',
                                    emptytext: '无',
                                    disabled: true
                                })
                            }
                        })
                        $('[data-name="change_price"]').each(function() {
                            let actrow = $('#changeTable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                            if (actrow && (actrow.change_state == 2 || actrow.change_state == 1)) {
                                $(this).editable({
                                    type: 'text',
                                    emptytext: '无',
                                    disabled: true
                                })
                            }
                        })
                    },
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        if (row.change_state == 2) {
                            alert("变更已审核通过，不能再进行修改");
                        } else {
                            common.rowModifyWithT(_self, orderDetailApiUrl + 'MaterielCrmControlSRV_modify', row, 'ordermateriel_id', $table)
                        }
                    }
                })
                common.changeTableClass($table)
            }

            function initTableMateriel() {
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: orderDetailApiUrl + 'MaterielCrmControlSRV_search_mat',
                    queryParams: queryParamsMateriel,
                    sidePagination: 'server',
                    ajaxOptions: function() {
                        let headers = {};
                        headers.authorization = common.getStoreData('token');
                        return {
                            headers: headers
                        }
                    },
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.actFormatter('act', actCheckBox, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormat('materiel_batch', '批次')
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {},
                    onEditableSave: function(field, row, oldValue, $el) {}
                })
                common.changeTableClass($tableSearch)
            }

            function initPage() {
                let params = {};
                params.order_id = _self.order.order_id;
                params.user_id = common.getStoreData('userinfo').user_id;
                $('#room_id').html('')
                common.initSelect2($('#room_id'), _self.order.roomsInfo);
                common.initSelect2($('#change_type_m'), _self.pagePara.changeTypeInfo);
                _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_init', params).then((response) => {
                    let retData = response.data.info;
                    _self.retData = JSON.parse(JSON.stringify(retData));
                    if (retData.staffType != null && retData.staffType.length > 0) {
                        for (var i = 0; i < retData.staffType.length; i++) {
                            if (retData.staffType[i].staff_type == 8) {
                                _self.staffType = retData.staffType[i].staff_type;
                            }
                        }
                    }
                    $('#formChange').parsley();
                    $('#formSubmit').parsley()
                    common.initDatepicker($('#change_pre_delivery_date'))
                    initTable();
                    initTableMateriel();
                    common.reSizeCall();
                    console.log('init success')
                }, (response) => {
                    console.log('init error');
                    common.dealErrorCommon(_self, response)
                })

            }

            $(function() {
                initPage()
            })
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#AddChangeModal').modal('show')
        },
        addAct: function() {
            let _self = this;
            let formChange = $('#formChange');
            if (formChange.parsley().isValid()) {
                for (var i = 0; i < _self.searchedRow.length; i++) {
                    _self.workRow = {}
                    _self.workRow.room_id = common.getSelect2Val('room_id');
                    _self.workRow.order_id = _self.order.order_id;
                    _self.workRow.template_id = _self.templateId;
                    _self.workRow.materiel_id = _self.searchedRow[i].materiel_id;
                    _self.workRow.materiel_batch = _self.searchedRow[i].materiel_batch;
                    _self.workRow.purchase_state = 1;
                    _self.workRow.change_flag = 1;
                    _self.workRow.change_type = $("#change_type_m").val()[0];
                    if ($("#change_type_m").val() == 2) {
                        //减项，金额存负数
                      if($("#change_price").val()>0){
                        _self.workRow.change_price = 0 - $("#change_price").val();
                      }else{
                        return common.dealPromptCommon('输入金额必须大于0')
                      }
                    } else if($("#change_type_m").val() == 1) {
                      if($("#change_price").val()>0){
                        _self.workRow.change_price = $("#change_price").val();
                      }else{
                        return common.dealPromptCommon('输入金额必须大于0')
                      }
                    } else {
                      _self.workRow.change_price = $("#change_price").val();
                    }
                    //审核状态：待审核
                    _self.workRow.change_state = 0;

                    _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_add', _self.workRow).then((response) => {
                        $('#changeTable').bootstrapTable("refresh", _self.queryParams)
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }

                $('#AddChangeModal').modal('hide')
                _self.refreshList()
            }

        },
        modAct: function(event) {
            let _self = this;
            if (!common.selectCheck(_self, 'materiel_state', '请选择采购状态')) return;
            common.rowModifyWithT(_self, orderDetailApiUrl + 'MaterielCrmControlSRV_modify', _self.workRow, 'materiel_id', $('#changeTable'));
            $('#AddChangeModal').modal('hide');
        },
        refreshList: function() {
            $('#changeTable').bootstrapTable("refresh")
        },
        checkState: function(state, event) {
            let _self = this;
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        searchMate: function(event) {
            let _self = this;
            _self.searchedRow = [];

            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#changeMaterialSelect').val();
                return JSON.stringify(params)
            }
            $('#changeTableSearch').bootstrapTable("refresh", queryParamsMateriel)
        },
        showPurchaseList: function() {
            let _self = this;
            //        this.$router.push({ path: '/crm/purchaseListControl' , query: {orderId: this.order.order_id}})
            _self.listType = 2
            _self.orderOD = JSON.parse(JSON.stringify(_self.order));
        },
        listenChanged: function(message) {
            console.log('listTypeChanged')
            let _self = this;
            _self.listType = 1
            _self.orderOD = JSON.parse(JSON.stringify(_self.order));
            $('#changeTable').bootstrapTable("refresh", queryParams)
        },
        showDueDate: function() {
            let _self = this
            if (_self.purchaseSelects.length <= 0) {
                return common.dealPromptCommon('请选择物料')
            }
            //                for (let i = 0; i < _self.purchaseSelects.length; i++) {
            //                    if (_self.purchaseSelects[i].materiel_amount <= 0) {
            //                        return common.dealPromptCommon('请填写物料数量')
            //                    }
            //                }
            $('#change_pre_delivery_date').datepicker('setDate', moment(new Date().getTime()).add(3, 'days').format('YYYY-MM-DD'))
            $('#changeDueModal').modal('show')
        },
        placePurchaseOrder: function() {
            let _self = this;
            let param = {}
            param.materials = _self.purchaseSelects
            param.order_id = _self.order.order_id
            param.pre_delivery_date = $('#change_pre_delivery_date').val()
            param.delivery_addr = $('#change_delivery_addr').val()
            param.change_flag = 1;
            _self.$http.post(orderDetailApiUrl + 'PurchaseControlSRV_add', param).then((response) => {
                common.dealPromptCommon('已加入采购列表')
                _self.purchaseSelects = []
                _self.refreshList()
                $('#changeDueModal').modal('hide')
            }, (response) => {
                common.dealErrorCommon(_self, response);
            });
        },
        search: function() {
            $('#changeTable').bootstrapTable("refresh", queryParams)
        },
        materialCheck: async function(event) {
            let _self = this;
            try {
                _self.workRow.order_id = _self.order.order_id;
                await _self.$http.post(orderDetailApiUrl + 'materialCheck', _self.workRow);
                _self.$parent.refreshOrder();
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        CheckChange: function(check_state, event) {
            let _self = this;
            if (!_self.check_message && check_state === '1') {
                common.dealPromptCommon('请填写审核意见')
                return
            }
            _self.workRow.ordermateriel_id = materialInfo.ordermateriel_id;
            _self.workRow.check_message = _self.check_message;
            _self.workRow.check_owner = '0';
            _self.workRow.check_state = check_state;
            _self.workRow.check_type = 'CHANGE';
            _self.workRow.checker_id = common.getStoreData('userinfo').user_id;
            _self.workRow.checker_name = common.getStoreData('userinfo').name;
            _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_check', _self.workRow).then((response) => {
                _self.refreshList();
                $('#changeModal').modal('hide')
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
      submitChange: function () {
        let _self = this
        if ($('#formSubmit').parsley().isValid()){
          let row = _self.oldRow
          row.change_state = 1;
          row.task_performer = common.getSelect2Val('task_performer_select')
          row.task_description = $('#task_description').val()
          let param = {
            'old': _self.oldRow,
            'new': row
          }
          _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_modify', param).then((response) => {
            common.dealSuccessCommon('变更审核已提交');
            $('#commitChangeModal').modal('hide')
            $('#changeTable').bootstrapTable('refresh')
          }, (error) => {
            common.dealErrorCommon(_self, error)
          })
        }
      }
    }
}
</script>
<style scoped>
.select-width {
    width: 150px;
}

.modal-center {
    display: flex;
    justify-content: center;
}
</style>
