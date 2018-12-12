<template>
<div class="panel panel-inverse">
    <div class="panel-body">
        <div class="col-md-12 no-padding">
            <div id="materielToolbar">
                <div class="form-inline" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input class="form-control" multiple id="search_text" placeholder="搜索物料编码、物料名称、规格型号" style="width: 230px;">
                        </div>
                        <div class="form-group">
                            <select class="select2 form-control select-width" id="materiel_source_a">
                                <option value="">请选择采购来源</option>
                                <option v-for="source in pagePara.materielSourceInfo" v-bind:value="source.id">{{source.text}}</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <select class="select2 form-control select-width" id="materielProcedure">
                                <option value="">请选择工序</option>
                                <option v-for="b in pagePara.materielProcedure" v-bind:value="b.id">{{b.text}}</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <button id="search" class="btn btn-info" v-on:click="search">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                    <div class="form-group pull-right">
                        <button class="btn btn-info" v-on:click="printMaterial">打印</button>
                        <button class="btn btn-info" v-on:click="bomSync" v-bind:disabled="checkState('SIGNED')">自动生成</button>
                        <button class="btn btn-info" v-on:click="download">下载模板</button>
                        <button class="btn btn-info" v-on:click="importMaterial" v-bind:disabled="checkState('SIGNED')">导入</button>
                        <button class="btn btn-info" v-on:click="addM" v-bind:disabled="checkState('SIGNED')">增加物料记录</button>
                        <button class="btn btn-info" v-on:click="addFromShop" v-bind:disabled="checkState('SIGNED')">商城</button>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-md-12 no-padding">
            <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                <button type="button" class="btn btn-white" v-for="room in order.roomsInfo" v-bind:class="{active: selectRoomID == room.id}" @click="roomTypeSwitch(room)">{{room.text}}</button>
            </div>
            <table id="materielTable"></table>
        </div>
    </div>

    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增物料单</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-10" style="padding-left: 0px">
                        <input class="form-control" v-model="mateRow.matNameOrCodeOrFormat" id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-2" style="padding-left: 140px">
                        <button type="button" class="btn btn-info" v-on:click="addAct">保存</button>
                    </div>
                    <table id="tableSearch"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="ImportMaterialModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">导入物料</h4>
                </div>

                <div class="modal-body">
                    <div class="form-group col-md-12">
                        <label>选择文件：</label><span>（请选.csv文件）</span>
                        <div>
                            <input type="file" id="materialFile" class="form-control input-sm" accept=".csv" size="1">
                            <span class="margin-top-ten">{{filePath}}</span>
                        </div>
                    </div>
                </div>
                <div class="modal-footer" style="border-top:none">
                    <button type="button" class="btn btn-info" id="importAct" v-on:click="importFile"><i class="fa fa-fw fa-plus"></i>导入</button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="ZoweePrecessModal">
        <div class="modal-dialog" style="width: 800px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">定制品采购</h4>
                </div>
                <form @submit.prevent="processAct" id="formProcess">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label>制造单编号</label>
                                <input class="form-control" v-model="processData.zoweeprocess_id" disabled>
                            </div>
                            <div class="form-group col-sm-12">
                                <label>制造单状态</label>
                                <input class="form-control" v-model="processData.zoweeprocess_state_text" disabled>
                            </div>
                            <div class="form-group col-sm-12">
                                <label><span class="table-required">*</span>制造单类型</label>
                                <select class="form-control select2" id="zoweeprocess_type" data-parsley-required="true"></select>
                            </div>
                            <div class="form-group col-sm-12">
                                <label><span class="table-required">*</span>期望交货期</label>
                                <input class="form-control" id="zoweeprocess_date" data-parsley-required="true">
                            </div>
                            <div class="form-group col-sm-12">
                                <label><span class="table-required">*</span>收货人姓名</label>
                                <input class="form-control" v-model="processData.zoweeprocess_name" data-parsley-required="true">
                            </div>
                            <div class="form-group col-sm-12">
                                <label><span class="table-required">*</span>收货人联系方式</label>
                                <input class="form-control" v-model="processData.zoweeprocess_phone" data-parsley-required="true">
                            </div>
                            <div class="form-group col-sm-12">
                                <label><span class="table-required">*</span>收货人地址</label>
                                <input class="form-control" v-model="processData.zoweeprocess_address" data-parsley-required="true">
                            </div>
                            <div class="form-group col-sm-12">
                                <label>备注</label>
                                <textarea class="form-control" v-model="processData.zoweeprocess_remark" rows="2"></textarea>
                            </div>
                            <div class="form-group col-sm-12">
                                <label>相关文件</label>
                                <div class="row col-sm-12">
                                    <span class="form-group btn-success btn-sm m-r-5 fileupload-button" v-show="processData.zoweeprocess_state ==='0' || processData.zoweeprocess_state ==='3'">
                                      <i class="fa fa-plus"></i>
                                      <input class="imageupload" id="processBtn" type="file" name="file">
                                    </span>
                                </div>
                                <div class="row col-sm-12" style="width: 795px">
                                    <table id="processFile"></table>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" id="processSubmit">提交</button>
                    </div>
                </form>
            </div>
        </div>
    </div>


    <iframe src='http://ncatest.pinmudo.com/home_nca' width='100' height='100' style="display:none"></iframe>
    <iframe src='http://shop.ncahouse.com/home_nca' width='100' height='100' style="display:none"></iframe>

</div>
</template>
<script>
const common = require('commonFunc');
const orderDetailApiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';
import Constant from '../../../../assets/js/constant'

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            templateId: '',
            delivery_addr: '',
            searchedRow: [],
            mateRow: {},
            orderOD: {},
            materialList: [],
            selectRoomID: 1,
            selectRoomType: '',
            materiel_source: '2',
            fileUrl: '',
            filePath: '',
            processData: {}
        }
    },
    name: 'NCAMaterialListControl',
    mounted: function() {
        common.initDatepicker($('#pre_delivery_date'))
        $('#formdue').parsley()
    },
    watch: {
        pagePara: function() {

        },
        order: function() {
            let _self = this
            _self.templateId = _self.order.template_id
            if (_self.order.roomsInfo.length > 0) {
                _self.selectRoomID = _self.order.roomsInfo[0].id
            }

            let $table = $('#materielTable');
            let $tableSearch = $('#tableSearch');
            let $processFile = $('#processFile');

            function actCheckBox(value, row) {
                return [
                    '<input type="checkbox" class="searchRow">'
                ].join('')
            }

            function actPurchaseSelect(value, row) {
                if (row.purchase_id) {
                    return [
                        '<input type="checkbox" class="purchaseSelect" disabled checked>'
                    ].join('')
                } else {
                    return [
                        '<input type="checkbox" class="purchaseSelect">'
                    ].join('')
                }
            }

            function deleteFormatter(value, row, index) {
                if (!row.purchase_id) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                    ].join('')
                }
            }

            function processFormatter(value, row, index) {
                if (row.materiel_amto == '2') {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 process">定制品采购</a>'
                    ].join('')
                }
            }

            function queryParams(params) {
                if ($('#search_purchase_state').val()) {
                    params.purchase_state = $('#search_purchase_state').val();
                }
                if ($('#materiel_source_a').val()) {
                    params.materiel_source = $('#materiel_source_a').val();
                }
                if ($('#search_text').val()) {
                    params.search_text = $('#search_text').val();
                }
                if ($('#materielProcedure').val()) {
                    params.materiel_procedure = $('#materielProcedure').val()[0];
                }
                params.room_id = _self.selectRoomID
                params.order_id = common.getUrlParams("orderId");
                return JSON.stringify(params);
            }

            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                return JSON.stringify(params)
            }

            window.tableEvents = {
                'click .delete': function(e, value, row, index) {
                    rowDelete(_self, '物料删除', orderDetailApiUrl, row, 'materiel_id')
                },
                'click .searchRow': function(e, value, row, index) {
                    if ($(this).attr("checked") == 'checked') {
                        _self.searchedRow.push(row)
                    } else {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    }
                },
                'click .process': async function(e, value, row, index) {
                    _self.workRow = JSON.parse(JSON.stringify(row))
                    await _self.processRefresh()
                    if (_self.processData.zoweeprocess_state === '0') { // 提交
                        $('#processSubmit').removeAttr("disabled")
                        $('#processSubmit').text('提交')
                    } else if (_self.processData.zoweeprocess_state === '3') { //重传
                        $('#processSubmit').removeAttr("disabled")
                        $('#processSubmit').text('重新提交')
                    } else {
                        $('#processSubmit').removeAttr("disabled")
                        $('#processSubmit').attr("disabled", "disabled")
                    }
                    $('#formProcess').parsley().reset()
                    $('#ZoweePrecessModal').modal('show')
                },
            };


            let rowDelete = function(_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function() {
                    _self.$http.post(apiUrl + 'MaterielCrmControlSRV_delete', row).then((response) => {
                        $('#materielTable').bootstrapTable("refresh", queryParams)
                        common.dealSuccessCommon('删除成功');
                        console.log('delete success');
                    }, (response) => {
                        console.log('delete error');
                        common.dealErrorCommon(_self, response);
                    });
                });
            };

            function initTable() {
                $table.bootstrapTable('destroy');
                let columns = []
                if (_self.checkState('SIGNED')) {
                    columns = [
                        common.actFormatter('checkAct', actPurchaseSelect, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('materiel_amount', '数量'),
                        // common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价格',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价格',common.priceFormat),
                        // common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '类型', 'roomMaterialinfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'room_type', '空间', 'roomTypeInfo'),
                        common.actFormatter('processAct', processFormatter, tableEvents)
                    ]
                } else {
                    columns = [
                        common.actFormatter('checkAct', actPurchaseSelect, tableEvents),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEditableRight('materiel_amount', '数量'),
                        // common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价格',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价格',common.priceFormat),
                        // common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '类型', 'roomMaterialinfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'room_type', '空间', 'roomTypeInfo'),
                        common.actFormatter('deleteAct', deleteFormatter, tableEvents)
                    ]
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: orderDetailApiUrl + 'MaterielCrmControlSRV_search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.materialList = res.info.rows;
                        return res.info;
                    },
                    columns: columns,
                    clickToSelect: true,
                    idField: 'ordermateriel_id',
                    uniqueId: 'ordermateriel_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_modify', {
                            'old': _self.oldRow,
                            'new': row
                        }).then((response) => {
                            $('#materielTable').bootstrapTable("refresh", queryParams)
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        })
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
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [{
                            field: 'state',
                            checkbox: true
                        },
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '类型', 'roomMaterialinfo'),
                        common.BTRowFormat('domain_name', '所属机构')
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {},
                    onEditableSave: function(field, row, oldValue, $el) {},
                    onCheckAll: function(rows) {
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchedRow.push(rows[i])
                        }
                    },
                    onUncheckAll: function(rows) {
                        _self.searchedRow = []
                    },
                    onCheck: function(row, $element) {
                        _self.searchedRow.push(row)
                    },
                    onUncheck: function(row, $element) {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    },
                    onPageChange: function(number, size) {
                        _self.searchedRow = []
                    }
                })
                common.changeTableClass($tableSearch)
            }

            function initProcessFileTable() {
                $processFile.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('file_type', '文件类型'),
                        common.BTRowFormatAlignLeft('file_name', '文件名称'),
                        common.BTRowFormatWithFormatter('file_path', '文件链接', common.linkFormatter)
                    ],
                    idField: 'file_id',
                    uniqueId: 'file_id',
                    clickToSelect: true,
                    locale: 'zh-CN'
                })
                common.changeTableClass($processFile)
            }

            function initPage() {
                _self.workRow.materiel_remark = _self.order.materiel_remark
                _self.pagePara.roomsInfo = $.extend(true, [], _self.order.roomsInfo)
                common.initSelect2($('#materiel_state'), _self.pagePara.purchaseTypeInfo);
                common.initSelect2($('#zoweeprocess_type'), _self.pagePara.zoweeProcessTypeInfo);
                initTable();
                initTableMateriel();
                initProcessFileTable();
                $('#formProcess').parsley();
                common.initDatepicker($('#zoweeprocess_date'));

                $('#processBtn').change(async function() {
                    let files = this.files
                    let maxsize = 2 * 1024 * 1024; //2M
                    if (files.length > 0) {
                        for (let i = 0; i < files.length; i++) {
                            if (files[i].size > maxsize) {　
                                alert('最大只允许上传2M的文件')　
                                return
                            }

                            let formData = new FormData();
                            formData.append('file', files[i])

                            _self.$http.post(orderDetailApiUrl + 'upload', formData).then((response) => {
                                _self.processData.files.push(response.data.info)
                                _self.processData.zoweeprocess_type = common.getSelect2Val('zoweeprocess_type')
                                _self.processData.zoweeprocess_date = $('#zoweeprocess_date').val()
                                _self.$http.post(orderDetailApiUrl + 'putZoweePrecess', {
                                    'process': _self.processData,
                                    'act': '0'
                                }).then((response) => {
                                    _self.processRefresh()
                                }, (response) => {
                                    console.log('modify error')
                                    common.dealErrorCommon(_self, response)
                                })
                            }, (response) => {
                                // error callback
                                common.dealErrorCommon(_self, response)
                            })
                        }
                    }
                })
            }

            $(function() {
                initPage()
            })
            this.initMaterialFormData();
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#materiel_state').val(null).trigger('change');
            $('#AddMaterielModal').modal('show')
            _self.searchedRow = []
            $("input[type=checkbox]").each(function() {
                $(this).prop("checked", false);
            })
        },
        addFromShop: async function (event) {
            let _self = this;
            let getUserParams='';
            let getParam='';
            let w = '';
            try {
                let websocket = common.createWebSocket();
                websocket.onclose = function (evt) {
                    $('#materielTable').bootstrapTable("refresh")
                };

                let paraJson = JSON.stringify({
                    orderId: _self.order.order_id,
                    roomsInfo: _self.order.roomsInfo,
                    authorization: common.getStoreData('token')
                })
                getParam = $.param({
                    json: paraJson
                });

                //跨域存cookie
                var _frm = document.createElement("iframe");
                _frm.style.display = "none";
                w = window.open();

                //获得采购端登陆信息
                let response = await _self.$http.post('/api/openapi/shop?method=login', {domain: document.domain});
                if (response) {
                    getUserParams = $.param({
                        userCookie: JSON.stringify(response.data.info)
                    });


                    if (document.domain === '112.74.29.64' || document.domain === '120.24.215.85') {
                        _frm.src = "http://ncatest.pinmudo.com/setCookie?" + getUserParams;
                        document.body.appendChild(_frm);

                        window.setTimeout(function () {
                            w.location = "http://ncatest.pinmudo.com/home_nca?" + getParam;
                        }, 2000);
                    } else if (document.domain === 'admin.ncahouse.com') {
                        _frm.src = "http://shop.ncahouse.com/setCookie?" + getUserParams;
                        document.body.appendChild(_frm);

                        window.setTimeout(function () {
                            w.location ="http://shop.ncahouse.com/home_nca?" + getParam;
                        }, 2000);
                    }
                }
            } catch (error) {
                w.close();
                common.dealErrorCommon(_self, error);
            }
        },
        addAct: function() {
            let _self = this;
            for (var i = 0; i < _self.searchedRow.length; i++) {
                _self.workRow = {}
                _self.workRow.order_id = common.getUrlParams("orderId")
                _self.workRow.room_id = _self.selectRoomID
                _self.workRow.template_id = _self.templateId
                _self.workRow.materiel_id = _self.searchedRow[i].materiel_id
                _self.workRow.materiel_batch = _self.searchedRow[i].materiel_batch
                _self.workRow.purchase_state = 1

                _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_add', _self.workRow).then((response) => {
                    $('#materielTable').bootstrapTable("refresh")
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            $('#AddMaterielModal').modal('hide')
            _self.refreshList()
        },
        refreshList: function() {
            let _self = this;
            $('#materielTable').bootstrapTable("refresh")
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
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                params.materiel_state = 1
                return JSON.stringify(params)
            }
            $('#tableSearch').bootstrapTable("refresh", queryParamsMateriel)
        },
        search: function() {
            let _self = this
            $('#materielTable').bootstrapTable("refresh")
        },
        roomTypeSwitch: function(room) {
            this.selectRoomID = room.id;
            this.selectRoomType = room.room_type;
            $('#materielTable').bootstrapTable("refresh")
        },
        materielsourceSwitch: function(value) {
            this.materiel_source = value
            $('#tableSearch').bootstrapTable("refresh")
        },
        initMaterialFormData: function() {
            let _self = this;
            $('#materialFile').change(function() {
                let files = this.files;
                if (files.length > 0) {
                    let formData = new FormData();
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(orderDetailApiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno === 0) {
                            _self.fileUrl = response.data.info.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });
        },
        download: function() {
            this.$http.post(orderDetailApiUrl + 'downloadTemplate', {
                type: 2
            }).then(response => {
                let downUrl = response.data.info;
                window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        importMaterial: function() {
            $('#ImportMaterialModal').modal('show')
            $('#materialFile').val('');
        },
        importFile: function() {
            if (!this.fileUrl) {
                return common.dealWarningCommon('请选择文件')
            }

            let params = {
                uploadurl: this.fileUrl,
                order_id: this.order.order_id
            }

            this.$http.post(orderDetailApiUrl + 'importMaterialAll', params).then((response) => {
                let retData = response.data.info;
                $('#materielTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
            }, (response) => {
                common.dealErrorCommon(this, response)
            });

            $('#ImportMaterialModal').modal('hide')
        },
        printMaterial: function() {
            Constant.setData('materielSourceInfo', this.pagePara.materielSourceInfo);
            Constant.setData('materialTypeInfo', this.pagePara.materialTypeInfo);
            Constant.setData('unitInfo', this.pagePara.unitInfo);
            this.$router.push({
                path: '/nca/ordermanage/NCAMaterialInvoiceControl',
                query: this.order
            });
        },
        bomSync: async function() {
            let _self = this;
            try {
                common.dealConfrimCommon('生成后之前的空间和物料将被清空,是否继续操作', function() {
                    _self.$http.post(orderDetailApiUrl + 'bom_sync', _self.order).then((response) => {
                        _self.$parent.refreshOrder();
                        common.dealSuccessCommon('生成成功');
                        $('#materielTable').bootstrapTable("refresh");
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                })
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        processRefresh: async function() {
            let _self = this
            let response = await _self.$http.post(orderDetailApiUrl + 'getZoweePrecess', {
                ordermateriel_id: _self.workRow.ordermateriel_id
            });
            let retData = response.data.info;
            _self.processData = JSON.parse(JSON.stringify(retData))
            $('#zoweeprocess_date').val(_self.processData.zoweeprocess_date)
            $('#zoweeprocess_type').val(_self.processData.zoweeprocess_type).trigger('change')
            $('#processFile').bootstrapTable('load', {
                data: retData.files
            })
        },
        processAct: async function() {
            let _self = this
            if ($('#formProcess').parsley().isValid()) {
                let act = ''
                if (_self.processData.zoweeprocess_state === '0') {
                    act = '1'
                } else if (_self.processData.zoweeprocess_state === '3') {
                    act = '2'
                }
                _self.processData.zoweeprocess_type = common.getSelect2Val('zoweeprocess_type')
                _self.processData.zoweeprocess_date = $('#zoweeprocess_date').val()
                _self.$http.post(orderDetailApiUrl + 'putZoweePrecess', {
                    'process': _self.processData,
                    'act': act
                }).then((response) => {
                    _self.processRefresh()
                }, (response) => {
                    console.log('modify error')
                    common.dealErrorCommon(_self, response)
                })
            }
        },
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
    }
}
</script>
<style scoped>
.select-width {
    width: 140px;
}
</style>
