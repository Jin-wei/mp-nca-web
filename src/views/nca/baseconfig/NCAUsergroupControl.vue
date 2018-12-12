<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">行政办公管理</a></li>
        <li class="active">岗位列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">岗位列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索岗位编号、岗位名称" id="user_search" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button id="search_user" class="btn btn-info" v-on:click="queryUserConfirm">编号排列</button>
                            <button id="addA" class="btn btn-info" v-on:click="addA">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="userGroupTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddUserGroupModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 v-if="workRow.position_id" class="modal-title">编辑岗位管理记录</h4>
                    <h4 v-else class="modal-title">新增岗位管理记录</h4>
                </div>
                <form @submit.prevent="submitA" id="formUG">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>对应角色</label>
                                <div class="col-md-8">
                                    <input class="form-control" name='usergroup_id' id="usergroup_id" data-parsley-required="true" disabled>
                                </div>
                                <div class="col-md-1 padding-top-7">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>
                                </div>
                            </div>

                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>岗位名称</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.position_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>所属部门</label>
                                <div class="col-md-8">
                                    <input class="form-control" name='department_id' id="department_id"  data-parsley-required="true" disabled>
                                </div>
                                <div class="col-md-1 padding-top-7">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">岗位编制</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.department_plan_num" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">上级岗位</label>
                                <div class="col-md-8">
                                    <input class="form-control" name='p_position_id' id="p_position_id" disabled>
                                </div>
                                <div class="col-md-1 padding-top-7">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(3)">选择</label>
                                </div>
                            </div>
                            <!--<div class="form-group">-->
                                <!--<label class="col-md-2 control-label">基本工资</label>-->
                                <!--<div class="col-md-9">-->
                                    <!--<input class="form-control" v-model="workRow.base_salary" maxlength="50" data-parsley-maxlength="50">-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group">-->
                                <!--<label class="col-md-2 control-label">能力工资</label>-->
                                <!--<div class="col-md-9">-->
                                    <!--<input class="form-control" v-model="workRow.capacity_salary" maxlength="50" data-parsley-maxlength="50">-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group">-->
                                <!--<label class="col-md-2 control-label">绩效工资</label>-->
                                <!--<div class="col-md-9">-->
                                    <!--<input class="form-control" v-model="workRow.performance_salary" maxlength="50" data-parsley-maxlength="50">-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group">-->
                                <!--<label class="col-md-2 control-label">实际工资</label>-->
                                <!--<div class="col-md-9">-->
                                    <!--<input class="form-control" v-model="workRow.actual_salary" maxlength="50" data-parsley-maxlength="50">-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group">-->
                                <!--<label class="col-md-2 control-label">实际人数</label>-->
                                <!--<div class="col-md-9">-->
                                    <!--<input class="form-control" v-model="workRow.department_actual_num" maxlength="50" data-parsley-maxlength="50">-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" @click="modFlag='1'" id="addUGAct">保存</button>
                        <button type="submit" class="btn btn-info" @click="modFlag='2'" id="modUGAct">修改</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <userGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userGroup>
    <userNewDepartment :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewDepartment>
    <userNewGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewGroup>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAUsergroupControl?method=';

import userGroup from './userGroup.vue';
import userNewDepartment from './userNewDepartment.vue';
import userNewGroup from './userNewGroup.vue';
export default {
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            pagePara: {},
            pagePara2:{},
            pagePara3:{},
            pagePara4:{},
            modFlag: '',
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            index: '',
            p_position_id: '',
            p_position_id2: '',
            usergroup_id: '',
            department_id: '',
            type: ''
        }
    },
    name: 'NCAUsergroupControl',
    components: {
        userGroup,
        userNewDepartment,
        userNewGroup
    },
    mounted: async function() {
        let _self = this;
        let $table = $('#userGroupTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail">修改</a>'
            ].join('')
        }

        function queryParams(params) {
            params.search_text = $('#user_search').val();
            params.type = _self.type;
            return JSON.stringify(params)
        }

        function initTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)

                    $("#usergroup_id").val(_self.workRow.usergroup_name).trigger('change')
                    $("#department_id").val(_self.workRow.department_name).trigger('change')
                    $("#p_position_id").val(_self.workRow.p_position_name).trigger('change')
                    _self.p_position_id = row.p_position_id;
                    _self.usergroup_id = row.usergroup_id;
                    _self.department_id = row.department_id;
                    console.log('_self.p_department_id',_self.department_id)
                    console.log('_self.p_department_id',_self.usergroup_id)
                    console.log('_self.p_department_id',_self.p_position_id)
                    $('#modUGAct').show()
                    $('#addUGAct').hide()
                    $('#formUG').parsley().reset()
                    $('#AddUserGroupModal').modal('show')
                }
            };

            $table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('position_id', '岗位编号'),
                    common.BTRowFormatAlignLeft('position_name', '岗位名称'),
                    common.BTRowFormat('department_id', '所属部门编号'),
                    common.BTRowFormatAlignLeft('department_name', '所属部门名称'),
                    common.BTRowFormatAlignRight('department_plan_num', '岗位编制'),
                    common.BTRowFormat('p_position_id', '上级岗位编号'),
                    common.BTRowFormatAlignLeft('p_position_name', '上级岗位名称'),
                    // common.BTRowFormat('base_salary', '基本工资标准'),
                    // common.BTRowFormat('capacity_salary', '能力工资标准'),
                    // common.BTRowFormat('performance_salary', '绩效工资标准'),
                    // common.BTRowFormat('actual_salary', '实际工资标准'),
                    // common.BTRowFormat('department_actual_num', '实际人数'),
                    common.BTRowFormat('usergroup_name', '对应角色'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ],
                sortOrder:'asc',
                idField: 'position_id',
                uniqueId: 'position_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'position_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            let user_id = _self.pagePara.userInfo.user_id

            initTable();
            $('#formUG').parsley()
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        addA: function(event) {
            let _self = this;
            _self.workRow = {};
            if (_self.p_position_id != null) {
                _self.p_position_id=null;
            }
            $('#usergroup_id').val(null).trigger('change');
            $('#department_id').val(null).trigger('change')
            $('#p_position_id').val(null).trigger('change')
            $('#modUGAct').hide();
            $('#addUGAct').show();
            $('#formUG').parsley().reset();
            $('#AddUserGroupModal').modal('show');
        },
        submitA: async function(event) {
            let _self = this;
            if ($('#formUG').parsley().isValid()) {
                try {
                    _self.workRow.usergroup_id = _self.pagePara2.userGroupId;
                    _self.workRow.department_id = _self.pagePara3.userDepartmentId;
                    _self.workRow.p_position_id = _self.p_position_id;

                    if (_self.modFlag === '1') {
                        this.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                            let retData = response.data.info;
                            _self.workRow={};
                            common.dealSuccessCommon('保存成功');
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        });
                    } else if (_self.modFlag === '2') {
                        _self.workRow.p_position_id = _self.p_position_id;
                        _self.workRow.usergroup_id =_self.usergroup_id;
                        _self.workRow.department_id =_self.department_id

                        console.log('_self.p_department_id',_self.department_id)
                        console.log('_self.p_department_id2',_self.usergroup_id)
                        console.log('_self.p_department_id2',_self.p_position_id)
                       common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'position_id', $('#userGroupTable'))
                    }
                    window.setTimeout(function () {
                        $('#userGroupTable').bootstrapTable("refresh");
                    },500);
                    console.log('success')
                    $('#formUG').parsley().reset();
                    $('#AddUserGroupModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
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
        queryConfirm: function(event) {
            $('#userGroupTable').bootstrapTable("refresh")
        },
        queryUserConfirm: function(event) {
            let _self = this;
            _self.type=1;
            $('#userGroupTable').bootstrapTable("refresh")
        },
        teamP: function(index) {
            let _self = this;
            _self.index = index;
            if (_self.index == 1) {
                _self.eventHub.$emit('show-userselect-dialog');
            } else if (_self.index == 2) {
                _self.eventHub.$emit('show-departmentselect-dialog');
            } else if (_self.index == 3) {
                _self.eventHub.$emit('show-usergroupselect-dialog');
            }
        },
        userSelectCallback2: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeGroup', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#usergroup_id").val(_self.pagePara2.userGroupName);
                    console.log('_self.pagePara2',_self.pagePara2)
                    _self.usergroup_id = _self.pagePara2.userGroupId
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        departmentSelectCallback: async function(users) {
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
        userGroupSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeUserGroup', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara4 = JSON.parse(JSON.stringify(retData));
                    $("#p_position_id").val(_self.pagePara4.userNewGroupName)
                    _self.p_position_id = _self.pagePara4.userNewGroupId
                    _self.p_position_id2 = _self.pagePara4.userNewGroupId
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
</style>
