<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">资产验收管理</a></li>
            <li class="active">固定资产验收单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading" style="position: relative">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">固定资产验收单</h4>
                        <span style="position: absolute;right:180px;top:12px;" id="AssetsInfo"></span>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text_AADC" placeholder="搜索固定资产编号、固定资产名称"
                                           style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info"
                                        v-show="check_state ==0 || check_state ==2 && check_flag == 0"
                                        v-on:click="addAA">增加
                                </button>
                                <button class="btn btn-info"
                                        v-show="check_state ==0 || check_state ==2 && check_flag == 0"
                                        v-on:click="saveAssetsA">提交
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="assetsAcceptanceDetailTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddAssetsAcceptanceModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增固定资产验收项记录</h4>
                    </div>
                    <form @submit.prevent="submitForm" id="formAA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>固定资产名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.fixedassets_name"
                                               id="fixedassets_name" data-parsley-required="true" maxlength="50"
                                               data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.fixedassets_model"
                                               id="fixedassets_model" data-parsley-required="true" maxlength="10"
                                               data-parsley-maxlength="10">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.fixedassets_unit"
                                               id="fixedassets_unit" data-parsley-required="true" maxlength="7"
                                               data-parsley-maxlength="7">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">预计使用年限</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.use_time_limit" id="use_time_limit"
                                               maxlength="7" data-parsley-maxlength="7" data-parsley-type="number">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>固定资产分类</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="fixedassets_category"
                                                data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">预计净残值率</label>
                                    <div class="col-sm-8 p_r">
                                        <input class="form-control" v-model="workRow.residual_value_rate"
                                               id="residual_value_rate" maxlength="7" data-parsley-maxlength="7">
                                        <span class="p_a">%</span>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>折旧方法</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="depreciation_category"
                                                data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>验收类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="fixedassetscheck_acceptance"
                                                data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-md-9" style="padding-left:12px">
                                        <input class="form-control" id="department_id" data-parsley-required="true"
                                               disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label" style="padding-right: 3px"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9" style="padding-left:12px">
                                        <input class="form-control" id="user_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(2)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px">附件</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input id="file" type="file" style="margin-top: 10px">
                                        <div style="margin-top: 10px">
                                            <p v-for="(file,index) in detail.files" style="font-size:16px">
                                                <a :href=file.file_url target="_blank">
                                                    <i class="glyphicon glyphicon-file" style="font-size:22px"></i>
                                                    {{file.file_name}}
                                                </a>
                                                <i class="glyphicon glyphicon-remove-sign delete_icon"
                                                   v-on:click="removeImage(index,file)"></i>
                                            </p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveRMAct">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>


        <div class="modal fade" id="AssetsAcceptanceModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">查看固定资产验收项记录</h4>
                    </div>
                    <form @submit.prevent="confirmForm" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">预计使用年限</label>
                                    <div class="col-sm-10">
                                        <input class="form-control" v-model="workRow.use_time_limit"
                                               id="show_use_time_limit" maxlength="7" data-parsley-maxlength="7"
                                               data-parsley-type="number" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">预计净残值率</label>
                                    <div class="col-sm-10 p_r">
                                        <input class="form-control" v-model="workRow.residual_value_rate"
                                               id="show_residual_value_rate" maxlength="7" data-parsley-maxlength="7"
                                               disabled>
                                        <span class="p_a">%</span>
                                    </div>
                                </div>
                            </div>
                            <!--<div class="row">-->
                            <!--<div class="form-group col-sm-12">-->
                            <!--<label class="col-sm-2 control-label"><span class="table-required">*</span>折旧方法</label>-->
                            <!--<div class="col-sm-10">-->
                            <!--<select class="form-control select2" id="show_epreciation_category" data-parsley-required="true" data-parsley-group="block-1"></select>-->
                            <!--</div>-->
                            <!--</div>-->
                            <!--</div>-->
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>归属部门编号</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_department_id" data-parsley-required="true"
                                               disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>归属部门名称</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="department_id_show" data-parsley-required="true"
                                               disabled>
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="form-group col-sm-12" style="width: 900px;padding-left: 19px">
                                        <label class="col-md-2 control-label"><span class="table-required">*</span>管理责任人</label>
                                        <div class="col-md-10">
                                            <input class="form-control" id="show_user_id" data-parsley-required="true"
                                                   disabled>
                                        </div>
                                        <!--<div class="col-md-1 padding-top-7">-->
                                        <!--<label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>-->
                                        <!--</div>-->
                                    </div>
                                </div>
                                <div class="row">
                                    <div class="form-group col-sm-12" style="padding-left: 18px">
                                        <label class="col-sm-2 control-label">附件</label>
                                        <div class="col-sm-10">
                                            <input id="fileUI" type="file" style="margin-top: 10px">
                                            <div style="margin-top: 10px">
                                                <p v-for="(file,index) in detail.files" style="font-size:16px">
                                                    <a :href=file.file_url target="_blank">
                                                        <i class="glyphicon glyphicon-file" style="font-size:22px"></i>
                                                        {{file.file_name}}
                                                    </a>
                                                    <i class="glyphicon glyphicon-remove-sign delete_icon"
                                                       v-on:click="removeImage(index,file)"></i>
                                                </p>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" class="btn btn-info" id="save">确认</button>
                            </div>
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

    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

    import userNewDepartment from '../baseconfig/userNewDepartment.vue';
    import userSelectDialog from '../../../components/common/userSelectDialog.vue'

    export default {
        data: function () {
            return {
                workRow: {},
                pagePara: {},
                pagePara2: {},
                pagePara3: {},
                fixedassetscheck_id: '',
                fixedassetscheck_no: '',
                apply: {},
                usergroupId: '',
                users: [],
                eventHub: new Vue(),
                index: '',
                mulityFlag: '',
                user_id: '',
                check_state: '',
                check_flag: '',
                detail: {
                    files: []
                },
                AssetsInfo: ''
            }
        },
        name: 'NCAAssetsAcceptanceDetailControl',
        components: {
            userNewDepartment,
            userSelectDialog
        },
        mounted: function () {
            let _self = this;
            let $assetsAcceptanceDetailTable = $('#assetsAcceptanceDetailTable');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                common.initSelect2($('#fixedassets_category'), _self.pagePara.fixedassetstype);
                common.initSelect2($('#depreciation_category'), _self.pagePara.depreciationmethodtype);
                common.initSelect2($('#fixedassetscheck_acceptance'), _self.pagePara.acceptancetype);
                _self.fixedassetscheck_id = common.getUrlParams('fixedassetscheck_id');
                _self.fixedassetscheck_no = common.getUrlParams('fixedassetscheck_no');

                response = await _self.$http.post(apiUrl + 'search_adt', {fixedassetscheck_id: _self.fixedassetscheck_id});
                _self.apply = JSON.parse(JSON.stringify(response.data.info));
                let retData = response.data.info;
                _self.check_state = _self.apply.check_state;
                _self.check_flag = _self.apply.check_flag;
                $('#check_state').val(_self.apply.check_state).trigger('change');

                if (_self.fixedassetscheck_no != null) {
                    let AssetsInfo = '验收单编号：' + _self.fixedassetscheck_no;
                    $("#AssetsInfo").html(AssetsInfo);
                }

                initAllDemandTable();
                common.reSizeCall();
                $('#formAA').parsley()
            }

            function queryParams(params) {
                params.search_text = $('#search_text_AADC').val();
                params.fixedassetscheck_id = _self.fixedassetscheck_id;
                return JSON.stringify(params)
            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 show_meeting">查看</a>',
                    '<a class="btn btn-info btn-xs m-r-5 meeting_delete">删除</a>'
                ].join('')
            }

            function initAllDemandTable() {
                window.tableEvents = {
                    'click .meeting_delete': function (e, value, row, index) {
                        if (_self.check_state == 1) {
                            return common.dealPromptCommon('该申请单已提交')
                        } else if (_self.check_state == 3) {
                            return common.dealPromptCommon('该申请单已通过')
                        }
                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_cd', $assetsAcceptanceDetailTable, row, 'fixedassetscheckdetail_id', function () {
                        })
                    },
                    'click .show_meeting': function (e, value, row, index) {
                        $('#AssetsAcceptanceModal').modal("show")
                        _self.oldRow = $.extend(true, {}, row)
                        _self.workRow = $.extend(true, {}, row)

                        $('#show_use_time_limit').val(_self.workRow.use_time_limit).trigger('change');
                        $('#show_residual_value_rate').val(_self.workRow.residual_value_rate).trigger('change');
                        $('#show_department_id').val(_self.workRow.department_id).trigger('change');
                        $('#department_id_show').val(_self.workRow.department_name).trigger('change');
                        // $('#fileUI').val(_self.workRow.files).trigger('change');
                        $('#show_user_id').val(_self.workRow.user_name).trigger('change');
                        _self.user_id = row.user_id;
                        _self.department_id2 = row.department_id;
                        _self.detail = row;
                    }
                };

                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'fixedassets_category', '固定资产分类', 'fixedassetstype'),
                    // common.BTRowFormat('use_time_limit', '预计使用年限'),
                    // common.BTRowFormat('residual_value_rate', '预计净残值率'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'depreciation_category', '折旧方法', 'depreciationmethodtype'),
                    // common.BTRowFormat('department_id', '归属部门编号'),
                    // common.BTRowFormatAlignLeft('department_name', '归属部门名称'),
                    common.BTRowFormat('created_at', '购入验收时间'),
                    // common.BTRowFormatWithFE('files', '附件', common.filesFormatter),
                    // common.BTRowFormatAlignLeft('user_name', '管理责任人'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'fixedassetscheck_acceptance', '验收类型', 'acceptancetype'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ];

                $assetsAcceptanceDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_cd',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'fixedassetscheckdetail_id',
                    uniqueId: 'fixedassetscheckdetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        // common.rowModifyWithT(_self, apiUrl + 'modify_doc', row, 'fixedassetscheckdetail_id', $assetsAcceptanceDetailTable)
                    }
                });
                common.changeTableClass($assetsAcceptanceDetailTable)
            }

            $(function () {
                initData();

                common.fileUpload(_self, $('#file'), apiUrl, function (fileInfo) {
                    let convert = {} //转换字段名
                    convert.file_name = fileInfo.name;
                    convert.file_url = fileInfo.url;
                    convert.file_type = fileInfo.type;
                    _self.detail.files.push(convert);
                    $('#file').val('');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            })
        },
        methods: {
            queryConfirm: function (event) {
                let _self = this;
                $('#assetsAcceptanceDetailTable').bootstrapTable("refresh")
            },
            addAA: function (data) {
                let _self = this;
                _self.workRow = {};
                $('#fixedassets_category').val(null).trigger('change');
                $('#depreciation_category').val(null).trigger('change');
                $('#fixedassetscheck_acceptance').val(null).trigger('change');
                $('#department_id').val(null).trigger('change');
                $('#user_id').val(null).trigger('change');
                _self.detail = {
                    files: []
                }
                $('#AddAssetsAcceptanceModal').modal('show');
            },
            teamP: function (index) {
                let _self = this;
                _self.index = index;
                if (_self.index == 1) {
                    _self.eventHub.$emit('show-departmentselect-dialog');
                } else if (_self.index == 2) {
                    _self.eventHub.$emit('show-userselect-dialog');
                }
            },
            departmentSelectCallback: async function (users) {
                let _self = this;
                try {
                    _self.$http.post(apiUrl + 'changeDepartment', {
                        user_id: _self.pagePara.userInfo.user_id,
                        users: users
                    }).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                        $("#department_id").val(_self.pagePara3.userDepartmentName)
                        _self.department_id = _self.pagePara3.userDepartmentId
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            userSelectCallback: async function (users) {
                let _self = this;
                let params = {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }
                try {
                    _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                        $("#user_id").val(_self.pagePara2.meetingName)
                        _self.user_id = _self.pagePara2.meetingId
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            saveAssetsA: function (event) {
                let _self = this;
                let params = {
                    fixedassetscheck_id: _self.fixedassetscheck_id,
                    fixedassetscheck_no: _self.fixedassetscheck_no
                };
                _self.$http.post(apiUrl + 'submit_c', params).then((response) => {
                    let retData = response.data.info;
                    _self.check_state = retData.check_state;
                    $('#purch_state').val(_self.check_state).trigger('change');
                    common.dealSuccessCommon('提交成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            },
            submitForm: async function (event) {
                if ($('#formAA').parsley().isValid()) {
                    let _self = this, params = $.extend(true, {}, _self.detail);
                    _self.workRow.files = [];
                    for (let i of _self.detail.files) {
                        if (!i.file_id) {
                            _self.workRow.files.push(i);
                        }
                    }

                    try {
                        _self.workRow.fixedassetscheck_id = _self.fixedassetscheck_id;
                        _self.workRow.fixedassets_category = common.getSelect2Val('fixedassets_category');
                        _self.workRow.depreciation_category = common.getSelect2Val('depreciation_category');
                        _self.workRow.fixedassetscheck_acceptance = common.getSelect2Val('fixedassetscheck_acceptance');
                        _self.workRow.department_id = _self.pagePara3.userDepartmentId;
                        _self.workRow.user_id = _self.user_id;

                        let response = await _self.$http.post(apiUrl + 'add_cd', _self.workRow)
                        let retData = response.data.info;
                        console.log('success')
                        $('#assetsAcceptanceDetailTable').bootstrapTable("refresh");
                        $('#formAA').parsley().reset();
                        $('#AddAssetsAcceptanceModal').modal('hide')
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            confirmForm: async function (event) {
                $('#AssetsAcceptanceModal').modal('hide')
            },
            removeImage: function (index, file) {
                let _self = this;
                _self.detail.files.splice(index, 1);
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
        top: 8px;
        right: 0px;
    }
</style>
