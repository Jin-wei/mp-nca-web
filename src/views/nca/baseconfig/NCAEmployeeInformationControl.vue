<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">人力资源管理</a></li>
            <li class="active">员工信息列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">员工信息列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索工号、姓名" style="width: 130px;">
                                <!--</div>-->
                                <!--<div class="form-group">-->
                                    <select class="select2 form-control select-width" id="employee_ingender">
                                        <option value="">请选择性别</option>
                                        <option v-for="state in pagePara.genderInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                    <select class="select2 form-control select-width" id="search_sorting">
                                        <option value="">请选择排列方式</option>
                                        <option v-for="state in pagePara.sortingInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                <!--</div>-->
                                <!--<div class="form-group">-->
                                    <div class="form-group">
                                        <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                                    </div>
                                    <div class="form-group">
                                        <span>-</span>
                                        <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                                    </div>
                                    <!--<input type="text" class="form-control" id="created_at" placeholder="时间" />-->
                                <!--</div>-->
                                <!--<div class="form-group">-->
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-on:click="queryUserConfirm">工号排列</button>-->
                                    <!--<button class="btn btn-info" v-on:click="queryTimeConfirm">入职时间排列</button>-->
                                <!--</div>-->
                                <!--<div class="form-group">-->
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="importM" class="btn btn-info" v-on:click="importM">
                                    导入
                                </button>
                                <button id="downloadM" class="btn btn-info" v-on:click="downloadM">
                                    下载模板
                                </button>
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加</button>
                            </div>

                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#inemployee " data-toggle="tab">
                                    <span>在职员工</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(7)">
                                <a href="#outemployee" data-toggle="tab">
                                    <span>离职员工</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="inemployee">
                                <table id="inEmployeeTable"></table>
                            </div>
                            <div class="tab-pane fade" id="outemployee" >
                                <table id="outEmployeeTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddEmployeeModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增员工</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>性别</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="gender" data-parsley-required="true" ></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>部门</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" name='p_usergroup_id' id="p_usergroup_id" data-parsley-required="true" disabled>
                                        <!--<select class="form-control select2" style="width:100%" id="p_usergroup_id" data-parsley-required="true" ></select>-->
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label padding-top-7"><span class="table-required">*</span>岗位</label>
                                    <div class="col-sm-8" >
                                        <div class="col-sm-9" style="padding-right: 5px;padding-left: 0px;">
                                            <input class="form-control" id="usergroup_id" data-parsley-required="true" disabled>
                                        </div>
                                        <div class="form-group col-sm-3 padding-top-7">
                                            <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                        </div>
                                        <!--<select class="form-control select2" style="width:100%" id="usergroup_id" data-parsley-required="true" ></select>-->
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>入职日期</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="entry_date" data-parsley-required="true" >
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>薪资模式</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="user_form" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveAct">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="OutModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">离职</h4>
                    </div>
                    <form @submit.prevent="saveOut" id="formE">
                        <div class="modal-body row">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">工号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="user_id" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="name" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">离职日期</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="departure_date" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">离职原因</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="departure_reason" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px">备注</label>
                                    <div class="col-sm-10" style="padding-left: 12px">
                                        <textarea class="form-control" maxlength="100" id="departure_remark" data-parsley-maxlength="100"></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div class="modal fade" id="ImportModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">导入员工</h4>
                    </div>

                    <div class="modal-body">
                        <div class="form-group col-md-12">
                            <label>选择文件：</label><span>（请选.csv文件）</span>
                            <div>
                                <input type="file" id="file" class="form-control input-sm " accept=".csv" size="1">
                                <input type="text" id="select1" hidden>
                                <!--<span class="margin-top-ten">{{filePath}}</span>-->
                            </div>
                        </div>

                    </div>
                    <div class="modal-footer" style="border-top:none">
                        <button type="button" class="btn btn-primary btn-info" id="importAct" v-on:click="importAct">导入</button>
                    </div>
                </div>
            </div>
        </div>
        <userDepartmentGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userDepartmentGroup>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';

import userDepartmentGroup from './userDepartmentGroup.vue'
export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            tableType:1,
            workRow:{},
            postArray: [],
            fileUrl: "",
            filePath: "",
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            type:''
        }
    },
    name: 'NCAEmployeeInformationControl',
    components: {
        userDepartmentGroup
    },
    mounted: function() {
        let _self = this;
        let $inEmployeeTable = $('#inEmployeeTable');
        let $outEmployeeTable = $('#outEmployeeTable');

        function operateFormatter(value, row, index) {
            if (_self.tableType == 1) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>',
                    '<a class="btn btn-info btn-xs m-r-5 accept_appointment">离职</a>'
                ].join('')
            } else {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>'
                ].join('')
            }
        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                if(_self.tableType==1){
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAEmployeeDetailControl',
                        query: {
                            user_id: row.user_id,
                            state:1
                        }
                    })
                }else if (_self.tableType==7){
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAEmployeeDetailControl',
                        query: {
                            user_id: row.user_id,
                            state: 0
                        }
                    })
                }
            },
            'click .accept_appointment': function (e, value, row, index) {
                _self.oldRow = $.extend(true, {}, row);

                $('#user_id').val(_self.oldRow.user_id);
                $('#name').val(_self.oldRow.name);
                $('#formE').parsley().reset()
                $('#OutModal').modal('show');
            }
        };
        //入职
        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            params.gender =$('#employee_ingender').val();
            // params.entry_date= $('#created_at').val();
            params.createdBTime= $('#start_date').val();
            params.createdETime= $('#end_date').val();
            params.state = '1';
            params.type = $('#search_sorting').val();
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('username', '用户名'),
                common.BTRowFormatAlignLeft('name', '姓名'),
                common.BTRowFormatEdSelect2Disabled(_self, 'gender', '性别', 'genderInfo'),
                common.BTRowFormatAlignLeft('department_name', '部门'),
                common.BTRowFormatAlignLeft('position_name', '岗位'),
                common.BTRowFormat('entry_date', '入职时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'state', '状态', 'userStation'),
                common.BTRowFormatEdSelect2Disabled(_self, 'user_form', '薪资模式', 'contractInfo'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $inEmployeeTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'user_id',
                uniqueId: 'user_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'user_id', $inEmployeeTable)
                }
            });
            common.changeTableClass($inEmployeeTable)
        }
        //离职
        function queryParamsGroup(params) {
            params.search_text=$('#search_text').val();
            params.gender =$('#employee_ingender').val();
            // params.entry_date= $('#created_at').val();
            params.createdBTime= $('#start_date').val();
            params.createdETime= $('#end_date').val();
            params.state = '0';
            params.type = _self.type;
            return JSON.stringify(params)
        }
        function initGroupOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('user_id', '工号'),
                common.BTRowFormatAlignLeft('name', '姓名'),
                common.BTRowFormatEdSelect2Disabled(_self, 'gender', '性别', 'genderInfo'),
                common.BTRowFormatAlignLeft('department_name', '部门'),
                common.BTRowFormatAlignLeft('position_name', '岗位'),
                // common.BTRowFormat('entry_date', '入职时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'state', '状态', 'userStation'),
                common.BTRowFormatEdSelect2Disabled(_self, 'user_form', '聘用形式', 'contractInfo'),
                common.BTRowFormat('departure_date', '离职时间'),
                common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'departure_reason', '离职原因', 'departureReason'),
                common.BTRowFormatWithFormatterAlignLeft('departure_remark', '备注',common.remarkFormatter),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $outEmployeeTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParamsGroup,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'user_id',
                uniqueId: 'user_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($outEmployeeTable)
        }

        async function initData() {
            // common.initDatepicker($('#created_at'));
            common.initDatepicker($('#start_date'));
            common.initDatepicker($('#end_date'));
            common.initDatepicker($('#entry_date'));
//            common.initDatepicker($('#departure_date'));
            $('#departure_date').datepicker({
                language: 'zh-CN',
                todayBtn: "linked",
                autoclose: true,
                todayHighlight: true,
                format: 'yyyy-mm-dd',
                endDate: new Date()
            });
            let entry_date = $('#entry_date').val()
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            let user_id = _self.pagePara.userInfo.user_id
//            common.initSelect2($('#p_usergroup_id'),retData.pGroupList);
            common.initSelect2($('#gender'), retData.genderInfo);
            common.initSelect2($('#user_form'), retData.contractInfo);
            common.initSelect2($('#departure_reason'), retData.departureReason);
            common.initSelect2($('#state'), retData.userStation);

            initFitOrderTable();
            initGroupOrderTable();
            common.reSizeCall();
            $('#formA').parsley()
        }


        $(function() {
            $('#file').change(function() {
                let files = this.files;
                let formData = new FormData();
                if(files.length>0){
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(apiUrl + 'upload_dr', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.fileUrl = response.data.info.uploadurl.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });
//            $('#p_usergroup_id').change(function() {
//                let mValue = $('#p_usergroup_id').val()
//                if(mValue){
//                    _self.$http.post(apiUrl + 'usergroup', {
//                        p_usergroup_id: mValue
//                    }).then((response) => {
//                        if (response.data.info.groupList) {
//                            common.initSelect2($('#usergroup_id'), response.data.info.groupList);
//                            $('#usergroup_id').val(null).trigger('change');
//                        } else {
//                            common.dealErrorCommon(_self, response)
//                        }
//                    }, (response) => {
//                        common.dealErrorCommon(_self, response)
//                    })
//                } else if (mValue==null){
//                    _self.$http.post(apiUrl + 'usergroup', {
//                        p_usergroup_id: mValue
//                    }).then((response) => {
//                        if (response.data.info.groupList) {
//                            $("#usergroup_id").empty();
//                        } else {
//                            common.dealErrorCommon(_self, response)
//                        }
//                    }, (response) => {
//                        common.dealErrorCommon(_self, response)
//                    })
//                }
//            });
            initData();
        })
    },
    methods: {
        showTable:function(index){
            let _self = this;
            _self.tableType=index;

            $('#entry_date').val('');//入职时间
            if(index === 1 ){
                $('#inEmployeeTable').bootstrapTable("refresh")
            } else if (index === 7 ){
                $('#outEmployeeTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#inEmployeeTable').bootstrapTable("refresh")
            } else if (_self.tableType === 7 ){
                $('#outEmployeeTable').bootstrapTable("refresh")
            }
        },
        // queryUserConfirm: function(event) {
        //     let _self = this;
        //     _self.type=1;
        //     if(_self.tableType === 1 ){
        //         $('#inEmployeeTable').bootstrapTable("refresh")
        //     } else if (_self.tableType === 7 ){
        //         $('#outEmployeeTable').bootstrapTable("refresh")
        //     }
        // },
        // queryTimeConfirm: function(event) {
        //     let _self = this;
        //     _self.type=2;
        //     if(_self.tableType === 1 ){
        //         $('#inEmployeeTable').bootstrapTable("refresh")
        //     } else if (_self.tableType === 7 ){
        //         $('#outEmployeeTable').bootstrapTable("refresh")
        //     }
        // },
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#usergroup_id').val(null).trigger('change');
            $('#gender').val(null).trigger('change');
            $('#p_usergroup_id').val(null).trigger('change');
            $('#user_form').val(null).trigger('change');
            $('#entry_date').val(null).trigger('change');
            $('#AddEmployeeModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formA').parsley().isValid()) {
                try {
                    _self.workRow.entry_date = $('#entry_date').val()
                    _self.workRow.gender = common.getSelect2Val('gender');
                    // _self.workRow.p_usergroup_id = _self.pagePara2.userDepartmentId;
                    _self.workRow.department_id = _self.pagePara2.userDepartmentId;
                    _self.workRow.position_id = _self.pagePara2.userPositionId;
                    _self.workRow.usergroup_id = _self.pagePara2.userGroupId;
                    _self.workRow.user_form = common.getSelect2Val('user_form');
                    _self.workRow.state = 1;

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    $('#inEmployeeTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formA').parsley().reset();
                    $('#AddEmployeeModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        saveOut: async function () {
            let _self = this;
            try {
                if ($('#formE').parsley().isValid()) {

                    let params = {
                        departure_date: $('#departure_date').val(),
                        username: $('#user_id').val(),
                        user_id: $('#user_id').val(),
                        departure_reason: $('#departure_reason option:selected').val(),
                        departure_remark: $('#departure_remark').val(),
                        state: 0
                    };

                    let result = await _self.$http.post(apiUrl + 'departure', params);

                    $('#OutModal').modal('hide');
                    $('#inEmployeeTable').bootstrapTable("refresh");
                    $('#formE').parsley().reset()
                }
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        },
        importM: function(event) {
            let _self = this;
            $('#ImportModal').modal('show')
        },
        importAct: function(event) {
            let _self = this;
            if (!_self.fileUrl) {
                return common.dealWarningCommon('请选择文件')
            }
            var params = {
                uploadurl: _self.fileUrl
            }
            _self.$http.post(apiUrl + 'importUser', params).then((response) => {
                let retData = response.data.info;
                $('#inEmployeeTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });

            $('#ImportModal').modal('hide')
        },
        downloadM: function(event) {
            let _self = this;
            _self.$http.post(apiUrl + 'downloadEmployee', {}).then((response) => {
                let downUrl = response.data.info;
                window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-departmentgroupselect-dialog');
        },
        departmentGroupSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'change_group', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#p_usergroup_id").val(_self.pagePara2.userDepartmentName)
                    $("#usergroup_id").val(_self.pagePara2.userPositionName)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
