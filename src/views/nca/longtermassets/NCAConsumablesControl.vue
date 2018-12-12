<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">低值易耗品列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">低值易耗品列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" :placeholder="searchPlaceholder"
                                           style="width: 260px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button v-show="selectState == 1 || selectState == 2" id="addM" class="btn btn-info"
                                        v-on:click="addM">
                                    增加
                                </button>
                                <button v-show="selectState == 3" class="btn btn-info"
                                        v-on:click="addFixedAssetsOrderAR">生成资产验收单
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <ul class="nav nav-tabs">
                            <li class="active"><a href="#default-tab" @click="stateTypeSwitch(0)" data-toggle="tab">低值易耗品数据管理</a>
                            </li>
                            <li><a href="#default-tab" @click="stateTypeSwitch(1)" data-toggle="tab">资产申购</a>
                            </li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(3)" data-toggle="tab">资产验收</a>
                            </li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(2)"
                                            data-toggle="tab">资产验收单列表</a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="default-tab">
                                <table id="table"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="DepartmentModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增资产申购列表</h4>
                    </div>
                    <form @submit.prevent="postAct" id="formA">
                        <div class="modal-body">

                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label"><span
                                            class="table-required">*</span>申购部门</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="department" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="choiceDepartment()">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">提交
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="detailModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">修改</h4>
                    </div>
                    <form @submit.prevent="editDetailData" id="formB">
                        <div class="modal-body">
                            <div class="row" style="width: 600px;">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>低值易耗品名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="detailData.consumables_name"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="detailData.consumables_specifications"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="detailData.consumables_unit"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>验收类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="acceptance_type"
                                                data-parsley-required="true"></select>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input class="form-control" id="administrator" data-parsley-required="true"
                                               disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="choiceGroup(1)">选择</label>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input class="form-control" id="department-edit" data-parsley-required="true"
                                               disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="choiceGroup(4)">选择</label>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span
                                            class="table-required"></span>实物照片</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input id="file" type="file" style="margin-top: 10px">
                                        <div v-for="(file,index) in detailData.files" class="col-sm-3 img-div">
                                            <a :href=file.file_url target="_blank">
                                                <img class="consumables-img" :src="file.file_url" alt="">
                                            </a>
                                            <i class="glyphicon glyphicon-remove-sign delete_icon"
                                               v-on:click="removeImage(index,file)"></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">修改
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="detailModalOrder">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">修改</h4>
                    </div>
                    <form @submit.prevent="editDetailOrderData" id="formOrder">
                        <div class="modal-body">
                            <div class="row" style="width: 600px">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>低值易耗品名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.consumables_name"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text" disabled>
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.consumables_specifications"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text" disabled>
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.consumables_unit"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"
                                               type="text" disabled>
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>验收类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="acceptance_type_order"
                                                data-parsley-required="true"></select>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-sm-9" style="padding-left: 12px;">
                                        <input class="form-control" id="administrator_order"
                                               data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="choiceGroup(2)">选择</label>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-sm-9" style="padding-left: 12px;">
                                        <input class="form-control" id="department_order" data-parsley-required="true"
                                               disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="choiceGroup(3)">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">修改
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <userNewDepartment :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewDepartment>
        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>

    </div>
</template>
<script>
    import Vue from 'vue'

    const common = require('commonFunc')
    const apiUrl = '/api/nca/longtermassets/NCAConsumablesControlSRV?method='
    import userNewDepartment from '../baseconfig/userNewDepartment.vue';
    import userSelectDialog from '../../../components/common/userSelectDialog.vue'

    export default {
        data: function () {
            return {
                pagePara: {},
                oldRow: '',
                selectState: 0,//0数据管理 1资产申购 2资产验收列表 3资产验收
                consumablesData: '',
                department: '',
                editData: {},
                detailData: {},
                searchPlaceholder: '搜索固定资产编号、固定资产名称',
                isEdit: false,
                usergroupId: '',
                users: [],
                eventHub: new Vue(),
                userinfo: common.getStoreData('userinfo'),
                department_id: '',
                administrator_id: '',
                workRow: {},
                oldworkRow: {},
                administrator_id_order: '',
                department_id_order: '',
                checkDetailConsumables: [],
                department_edit_id: ''
            }
        },
        name: 'NCAConsumablesControlSRV',
        components: {
            userNewDepartment,
            userSelectDialog
        },
        mounted: function () {
            let _self = this;

            async function initData() {
                await _self.initData();
                _self.initTable();
            }

            $(function () {
                initData();
                common.reSizeCall();

                common.fileUpload(_self, $('#file'), apiUrl, function (fileInfo) {
                    let convert = {} //转换字段名
                    convert.file_name = fileInfo.name;
                    convert.file_url = fileInfo.url;
                    convert.file_type = fileInfo.type;
                    _self.detailData.files.push(convert);
                    $('#file').val('');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

            })
        },
        methods: {
            initData: async function () {
                let _self = this;
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                    common.initSelect2($('#acceptance_type'), _self.pagePara.LOW_VALUE_ACCEPTANCE_TYPE);
                    common.initSelect2($('#acceptance_type_order'), _self.pagePara.LOW_VALUE_ACCEPTANCE_TYPE);
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            },
            initTable: function () {
                let $table = $('#table');
                let _self = this;
                window.tableEvents = {
                    'click .show_detail': function (e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/longtermassets/NCAConsumablesDetailControl',
                            query: {
                                consumablesCode: row.consumables_code
                            }
                        })
                    },
                    "click .delete": function (e, value, row, index) {
                        common.rowDeleteWithApi(_self, '确认删除吗？', apiUrl + 'remove', $table, row, 'consumables_id')
                    },
                    "click .department-edit": function (e, value, row, index) {
                        _self.isEdit = true;
                        _self.editData = row;
                        $('#department').val(row.department_name);
                        $('#DepartmentModal').modal('show');
                    },
                    "click .detail-edit": function (e, value, row, index) {
                        _self.isEdit = true;
                        _self.detailData = row;
                        _self.administrator_id = row.consumables_administrator_id;
                        $('#administrator').val(row.name);
                        $('#department-edit').val(row.department_name);
                        $('#acceptance_type').val(row.consumables_acceptance_type_id).trigger('change');
                        $('#detailModal').modal('show');
                    },
                    'click .consumablesSelect': function (e, value, row, index) {
                        if ($(this).prop('checked')) {
                            _self.checkDetailConsumables.push(row);
                        } else {
                            _self.checkDetailConsumables.splice($.inArray(row, _self.checkDetailConsumables), 1);
                        }
                    },
                    "click .detail-edit-order": function (e, value, row, index) {
                        _self.isEdit = true;
                        _self.oldworkRow = $.extend(true, {}, row);
                        _self.workRow = $.extend(true, {}, row);

                        $('#acceptance_type_order').val(_self.workRow.consumables_acceptance_type_id).trigger('change');
                        $('#department_order').val(_self.workRow.department_name).trigger('change');
                        $('#administrator_order').val(_self.workRow.name).trigger('change');
                        _self.administrator_id_order = row.consumables_administrator_id;
                        _self.department_id_order = row.department_id;
                        $('#formOrder').parsley().reset();
                        $('#detailModalOrder').modal('show');
                    }
                }

                let statusFormat = function (value, row) {
                    for (let i of _self.pagePara.LOW_VALUE_STATUS) {
                        if (i.id === value) {
                            return i.text;
                        }
                    }
                };

                let typeFormat = function (value, row) {
                    for (let i of _self.pagePara.LOW_VALUE_ACCEPTANCE_TYPE) {
                        if (i.id === value) {
                            return i.text
                        }
                    }
                }

                let flagFormat = function (value) {
                    for (let i of _self.pagePara.SCRAPTYPE) {
                        if (i.id === value) {
                            return i.text
                        }
                    }
                }

                function queryParams(params) {
                    params.search_text = $('#search_text').val()
                    params.consumables_type_id = _self.selectState.toString()
                    return JSON.stringify(params)
                }

                function actFormatter(value, row) {
                    //资产验收列表
                    if ((row.consumables_status === '0' || row.consumables_status === '2') && _self.selectState == 2) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                        ].join('')
                    }
                    //资产申购
                    else if ((row.consumables_status === '0' || row.consumables_status === '2') && _self.selectState == 1) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 department-edit">修改</a>' +
                            '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                        ].join('')
                    }
                    //数据管理
                    else if (_self.selectState == 0) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 detail-edit">修改</a>'
                        ].join('')
                    }
                    //资产验收
                    else if (_self.selectState == 3) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 detail-edit-order">修改</a>'
                        ].join('')
                    }
                }

                function codeFormatter(value, row) {
                    return [
                        '<a class="show_detail" style="cursor: pointer" title="查看">',
                        value,
                        '</a>'
                    ].join('')
                }

                let columns;
                let url;
                if (_self.selectState == 0) {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                        common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                        common.BTRowFormat('consumables_specifications', '规格型号'),
                        common.BTRowFormat('consumables_unit', '计量单位'),
                        common.BTRowFormatWithFE('files', '实物照片', common.filesFormatter),
                        common.BTRowFormatAlignLeft('name', '管理责任人'),
                        common.BTRowFormatWithFormatter('consumables_acceptance_type_id', '验收类型', typeFormat),
                        common.BTRowFormatWithFormatter('scrap_flag', '报废标志', flagFormat),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]

                    url = apiUrl + 'search_detail';
                } else if (_self.selectState == 1) {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('consumables_code', '申购单编号', codeFormatter, tableEvents),
                        common.BTRowFormatAlignLeft('department_name', '申请部门'),
                        common.BTRowFormat('created_at', '申请时间'),
                        common.BTRowFormatWithFormatter('consumables_status', '状态', statusFormat),
                        common.BTRowFormatAlignLeft('name', '审批人'),
                        common.BTRowFormat('consumables_confirm_time', '审批时间'),
                        common.BTRowFormatWithFormatterAlignLeft('consumables_rejected_description', '驳回原因', common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url = apiUrl + 'search';
                } else if (_self.selectState == 2) {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFE('consumables_code', '资产验收单编号', codeFormatter, tableEvents),
                        common.BTRowFormatAlignLeft('consumables_creator_name', '验收人'),
                        common.BTRowFormatWithFormatter('consumables_status', '状态', statusFormat),
                        common.BTRowFormat('created_at', '申请时间'),
                        common.BTRowFormatAlignLeft('name', '审批人'),
                        common.BTRowFormat('consumables_confirm_time', '审批时间'),
                        common.BTRowFormatWithFormatterAlignLeft('consumables_rejected_description', '驳回原因', common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url = apiUrl + 'search';
                } else if (_self.selectState == 3) {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.actFormatter('checkAct', () => {
                            return `<input type="checkbox" class="consumablesSelect">`
                        }, tableEvents),
                        common.BTRowFormat('consumables_parent_code', '资产申购编号'),
                        common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                        common.BTRowFormat('consumables_specifications', '规格型号'),
                        common.BTRowFormat('consumables_unit', '计量单位'),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url = '/api/nca/longtermassets/NCAConsumablesDetailControlSRV?method=' + 'search_pdc';
                }

                $table.bootstrapTable('destroy');
                $table.bootstrapTable({
                    method: 'POST',
                    url: url,
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    idField: 'consumables_id',
                    uniqueId: 'consumables_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    undefinedText: '',
                    onPostBody: function () {
                    },
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'appoint_id', $table)
                    },
                    onPostBody: function () {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($table)
            },
            addM: function (event) {
                let _self = this;
                _self.isEdit = false;
                _self.editData = {};
                _self.workRow = {}
                $('#department').val(null);
                _self.department_id = '';
                if (_self.selectState == 1) {
                    $('#DepartmentModal').modal('show')
                } else if (_self.selectState == 2) {
                    common.dealConfrimCommon('确认新增易耗品资产验收申请？', async () => {
                        _self.addAct();
                    })
                }
            },
            postAct: function () {
                let _self = this;
                if (_self.isEdit) {
                    _self.modifyAct();
                } else {
                    _self.addAct();
                }
            },
            modifyAct: function (event) {
                let _self = this;
                let params = {
                    consumables_id: _self.editData.consumables_id,
                    consumables_department_id: _self.department_id
                };
                _self.$http.post(apiUrl + 'modify', params).then((response) => {
                    $('#DepartmentModal').modal('hide');
                    common.dealSuccessCommon('修改成功')

                    window.setTimeout(function () {
                        $('#table').bootstrapTable("refresh", _self.queryParams);
                    }, 10)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            addAct: function (event) {
                let _self = this;
                let params = {
                    consumables_type_id: _self.selectState.toString(),
                    consumables_department_id: _self.department_id
                };
                _self.$http.post(apiUrl + 'add', params).then((response) => {
                    $('#DepartmentModal').modal('hide');
                    common.dealSuccessCommon('增加成功')

                    window.setTimeout(function () {
                        _self.$router.push({
                            path: '/nca/longtermassets/NCAConsumablesDetailControl',
                            query: {
                                consumablesCode: response.data.info
                            }
                        })
                    }, 100)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            search: function () {
                let _self = this;
                $('#table').bootstrapTable("refresh", _self.queryParams)
            },
            stateTypeSwitch: function (value) {
                let _self = this
                $('#search_text').val('');
                if (value == 0) {
                    _self.searchPlaceholder = '搜索低值易耗品编号、低值易耗品名称';
                } else if (value == 1) {
                    _self.searchPlaceholder = '搜索申购单编号、申请部门';
                } else if (value == 2) {
                    _self.searchPlaceholder = '搜索资产验收单编号';
                } else if (value == 3) {
                    _self.checkDetailConsumables = [];
                    _self.searchPlaceholder = '搜索资产申购编号';
                }
                _self.selectState = value;
                _self.initTable();
            },
            editDetailData: function () {
                let _self = this;
                if ($('#formB').parsley().isValid()) {
                    let params = $.extend(true, {}, _self.detailData);
                    let acceptance_type = common.getSelect2Val('acceptance_type');
                    params.consumables_administrator_id = _self.administrator_id;
                    params.department_id = _self.department_edit_id;
                    params.consumables_acceptance_type_id = acceptance_type;
                    params.files = [];
                    for (let i of _self.detailData.files) {
                        if (!i.file_id) {
                            params.files.push(i);
                        }
                    }

                    _self.$http.post(apiUrl + 'modify_detail', params).then((response) => {
                        $('#detailModal').modal('hide');
                        common.dealSuccessCommon('提交成功！')
                        $('#formB').parsley().reset();

                        window.setTimeout(function () {
                            $('#table').bootstrapTable("refresh", _self.queryParams);
                        }, 10)
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            },
            removeImage: function (index, file) {
                let _self = this;
                _self.detailData.files.splice(index, 1);
                if (file.file_id) {
                    var params = {
                        file_id: file.file_id
                    }
                    _self.$http.post(apiUrl + 'removeFile', params).then(response => {
                        if (response.body.errno == 0) {
                        } else {
                            console.log(response.msg)
                        }
                    }, (response) => {
                        common.dealErrorCommon(response);
                    })
                }
            },
            choiceDepartment: function (index) {
                let _self = this;
                _self.eventHub.$emit('show-departmentselect-dialog');
            },
            choiceGroup: function (index) {
                let _self = this;
                _self.index = index;
                if (_self.index == 1) {
                    _self.eventHub.$emit('show-userselect-dialog');
                } else if (_self.index == 2) {
                    _self.eventHub.$emit('show-userselect-dialog');
                } else if (_self.index == 3) {
                    _self.eventHub.$emit('show-departmentselect-dialog');
                } else if (_self.index == 4) {
                    _self.eventHub.$emit('show-departmentselect-dialog');
                }
            },
            departmentSelectCallback: async function (users) {
                let _self = this;
                try {
                    let currentUser = common.getStoreData('userinfo');
                    if (users.length == 0) {
                        return common.dealWarningCommon('请选择部门！');
                    }
                    let params = {
                        user_id: currentUser.user_id,
                        users: users
                    }
                    let response = await _self.$http.post(apiUrl + 'changeDepartment', params);
                    if (_self.index == 3) {
                        $("#department_order").val(response.data.info.userDepartmentName)
                        _self.department_id_order = response.data.info.userDepartmentId
                    } else if (_self.index == 4) {
                        $("#department-edit").val(response.data.info.userDepartmentName)
                        _self.department_edit_id = response.data.info.userDepartmentId
                    } else {
                        $("#department").val(response.data.info.userDepartmentName)
                        _self.department_id = response.data.info.userDepartmentId
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            userSelectCallback: async function (users) {
                let _self = this;
                try {
                    let currentUser = common.getStoreData('userinfo');
                    if (users.length == 0) {
                        return common.dealWarningCommon('请选择管理员！');
                    }
                    let params = {
                        user_id: currentUser.user_id,
                        users: users
                    }
                    let response = await _self.$http.post(apiUrl + 'changeGroup', params);
                    if (_self.index == 1) {
                        $("#administrator").val(response.data.info.userName)
                        _self.administrator_id = response.data.info.userId
                    } else if (_self.index == 2) {
                        $("#administrator_order").val(response.data.info.userName)
                        _self.administrator_id_order = response.data.info.userId;
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            addFixedAssetsOrderAR: async function () {
                let _self = this;
                let tableData = this.checkDetailConsumables;

                let consumablesDetailId = '';
                for (let j in tableData) {
                    consumablesDetailId = consumablesDetailId + tableData[j].consumables_detail_id + ',';
                }

                consumablesDetailId = (consumablesDetailId.slice(consumablesDetailId.length - 1) == ',') ? consumablesDetailId.slice(0, -1) : consumablesDetailId;

                if (tableData.length === 0) {
                    return common.dealWarningCommon('请选择低值易耗品资产！');
                }

                for (let a = 0; a < tableData.length; a++) {
                    if (tableData[a].consumables_acceptance_type_id == '' || tableData[a].consumables_administrator_id == '' || tableData[a].department_id == '') {
                        return common.dealWarningCommon('请完善低值易耗品资产！');
                        _self.checkDetailConsumables = [];
                    }
                }
                let params = {
                    checkDetailConsumables: this.checkDetailConsumables,
                    purchDetailIds: consumablesDetailId,
                    scrap_type: 1
                }
                _self.$http.post('/api/nca/longtermassets/NCAConsumablesDetailControlSRV?method=' + 'submit_pdc', params).then((response) => {
                    let retData = response.data.info;
                    $('#table').bootstrapTable("refresh");
                    common.dealSuccessCommon('保存成功');
                    this.checkDetailConsumables = [];
                    consumablesDetailId = ''
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });
            },
            editDetailOrderData: function () {
                if ($('#formOrder').parsley().isValid()) {
                    let _self = this;
                    let acceptance_type = common.getSelect2Val('acceptance_type_order');
                    try {
                        _self.workRow.consumables_acceptance_type_id = acceptance_type;
                        _self.workRow.consumables_administrator_id = _self.administrator_id_order;
                        _self.workRow.department_id = _self.department_id_order;

                        common.rowModifyWithT(_self, '/api/nca/longtermassets/NCAConsumablesDetailControlSRV?method=' + 'modify_pdc', _self.workRow, 'docdetail_id', $('#table'));
                        console.log('success');
                        window.setTimeout(function () {
                            $('#table').bootstrapTable("refresh");
                        }, 500);
                        _self.workRow = {};
                        _self.checkDetailConsumables = [];
                        $('#detailModalOrder').modal('hide')
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            }
        }
    }
</script>
<style scoped>
    .select-width {
        width: 180px;
    }

    .consumables-img {
        width: 70px;
        height: 70px;
    }

    .img-div {
        margin-top: 10px;
        position: relative;
        font-size: 16px
    }

    .delete_icon {
        position: absolute;
        right: 0;
        top: -10px;
        z-index: 1;
        cursor: pointer;
    }
</style>
