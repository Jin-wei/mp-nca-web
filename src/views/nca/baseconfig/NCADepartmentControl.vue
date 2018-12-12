<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">行政办公管理</a></li>
        <li class="active">部门列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">部门列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索部门编号、部门名称" id="department_search" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>

                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button id="search_user" class="btn btn-info" v-on:click="queryUserConfirm">编号排列</button>
                            <button id="addA" class="btn btn-info" v-on:click="addA">增加</button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="departmentTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddDepartmentModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 v-if="workRow.department_id" class="modal-title">编辑部门记录</h4>
                    <h4 v-else class="modal-title">新增部门记录</h4>
                </div>
                <form @submit.prevent="submitA" id="formAH">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>部门名称</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.department_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label" ><span class="table-required">*</span>管理架构层级</label>
                                <div class="col-md-9" >
                                    <input class="form-control" v-model="workRow.department_level" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label" >上级部门名称</label>
                                <div class="col-md-8">
                                    <input class="form-control" name='p_department_id' id="p_department_id" disabled>
                                </div>
                                <div class="col-md-1 padding-top-7">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                </div>
                            </div>

                            <div class="form-group">
                                <label class="col-md-2 control-label">部门编制</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.department_plan_num" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group" v-show="modifyFlag==1">
                                <label class="col-md-2 control-label">状态</label>
                                <div class="col-md-9">
                                    <select class="form-control select2" style="width:100%" id="department_state"> </select>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" @click="modFlag='1'" id="addAct">保存</button>
                        <button type="submit" class="btn btn-info" @click="modFlag='2'" id="modAct">修改</button>

                    </div>
                </form>
            </div>
        </div>
    </div>
    <userNewDepartment :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewDepartment>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADepartmentControl?method=';

import userNewDepartment from './userNewDepartment.vue'
export default {
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            pagePara: {},
            pagePara2: {},
            modFlag: '',
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            p_department_id: '',
            p_department_id2: '',
            type: '',
            modifyFlag: 0,
        }
    },
    name: 'NCADepartmentControl',
    components: {
        userNewDepartment
    },
    mounted: async function() {
        let _self = this;
        let $table = $('#departmentTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail">修改</a>'
            ].join('')
        }

        function queryParams(params) {
            params.search_text = $('#department_search').val();
            params.type = _self.type;
            return JSON.stringify(params)
        }

        function initTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row);
                    _self.workRow = $.extend(true, {}, row);
                    _self.modifyFlag = 1;
                    $("#p_department_id").val(_self.workRow.p_department_name).trigger('change');
                    $('#department_state').val(_self.workRow.department_state).trigger('change')
                    _self.p_department_id2 = row.p_department_id;
                    console.log('_self.p_department_id2',_self.p_department_id2)
                    $('#modAct').show()
                    $('#addAct').hide()
                    $('#formAH').parsley().reset()
                    $('#AddDepartmentModal').modal('show')
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
                    common.BTRowFormat('department_id', '部门编号'),
                    common.BTRowFormatAlignLeft('department_name', '部门名称'),
                    common.BTRowFormat('department_level', '管理架构层级'),
                    common.BTRowFormatAlignLeft('p_department_name', '上级部门名称'),
                    common.BTRowFormatAlignRight('department_plan_num', '部门编制'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'department_state', '状态', 'userState'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ],
                sortOrder:'asc',
                idField: 'department_id',
                uniqueId: 'department_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'department_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            $('#domain_select').on('change', function(evt) {
                _self.queryConfirm()
            });
            // common.initSelect2($('#hr_checker_id'), _self.pagePara.staffInfo)
            let user_id = _self.pagePara.userInfo.user_id

            initTable();
            $('#formAH').parsley()
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        addA: function(event) {
            let _self = this;
            _self.modifyFlag = 0;
            _self.workRow = {};
            if (_self.p_department_id != null) {
                _self.p_department_id=null;
            }
            $('#p_department_id').val(null).trigger('change');
            $('#modAct').hide();
            $('#addAct').show();
            $('#formAH').parsley().reset();
            $('#AddDepartmentModal').modal('show');
        },
        submitA: async function(event) {
            let _self = this
            if ($('#formAH').parsley().isValid()) {
                try {
                    _self.workRow.p_department_id = _self.p_department_id

                    if (_self.modFlag === '1') {
                        this.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                            let retData = response.data.info;
                            _self.workRow={};
                            common.dealSuccessCommon('保存成功');
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        });
                    } else if (_self.modFlag === '2') {
                        _self.workRow.p_department_id = _self.p_department_id2;
                        _self.workRow.department_state = common.getSelect2Val('department_state');
                        console.log('_self.p_department_id2',_self.p_department_id2)
                       common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'department_id', $('#departmentTable'))
                    }
                    window.setTimeout(function () {
                        $('#departmentTable').bootstrapTable("refresh");
                    },500);

                    $('#formAH').parsley().reset();
                    console.log('success')
                    $('#AddDepartmentModal').modal('hide')
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
                common.initSelect2($('#department_state'), retData.userState);
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        queryConfirm: function(event) {
            $('#departmentTable').bootstrapTable("refresh")
        },
        queryUserConfirm: function(event) {
            let _self = this;
            _self.type=1;
            $('#departmentTable').bootstrapTable("refresh")
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-departmentselect-dialog');
        },
        departmentSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeGroup', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#p_department_id").val(_self.pagePara2.userDepartmentName)
                    _self.p_department_id = _self.pagePara2.userDepartmentId;
                    _self.p_department_id2 = _self.pagePara2.userDepartmentId
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
