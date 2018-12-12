<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">人力资源管理</a></li>
        <li class="active">人力需求列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">人力需求列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="created_at" placeholder="发布时间"/>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索岗位名称" id="spost_title" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button id="addA" class="btn btn-info" v-on:click="addA">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="humanTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddHumanModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增人力需求</h4>
                </div>
                <form @submit.prevent="submitA" id="formAH">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>部门</label>
                                <div class="col-md-9">
                                    <input class="form-control" name='post_usergroup_id' id="post_usergroup_id" data-parsley-required="true" disabled>
                                    <!--<input class="form-control" v-model="workRow.post_usergroup_id" name='post_usergroup_id' id="post_usergroup_id" data-parsley-required="true" disabled>-->
                                    <!--<select class="form-control select2" style="width:100%" id="post_usergroup_id" data-parsley-required="true"></select>-->
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>岗位名称</label>
                                <div class="col-md-8">
                                    <input class="form-control" id="post_title" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    <!--<select class="form-control select2" style="width:100%" id="post_title"></select>-->
                                </div>
                                <div class="form-group col-sm-1" style="padding: 7px 0 0 14px">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>指派人</label>
                                <div class="col-md-9">
                                    <select class="form-control select2" style="width:100%" id="hr_checker_id" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">描述</label>
                                <div class="col-md-9">
                                    <textarea class="form-control" v-model="workRow.hr_remark" maxlength="200" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" @click="modFlag='1'" id="addAct">提交</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <userDepartmentGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userDepartmentGroup>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAHumanResourceControl?method=';

import userDepartmentGroup from './userDepartmentGroup.vue'
export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            pagePara2:{},
            modFlag: '',
            usergroupId: '',
            users: [],
            eventHub: new Vue()
        }
    },
    name: 'NCAHumanResourceControl',
    components: {
        userDepartmentGroup
    },
    mounted: async function() {
        let _self = this;
        let $table = $('#humanTable');

        function queryParams(params) {
            let domain_id = common.getSelect2Val('domain_select')
            if (domain_id) {
                params.domain_id = domain_id
            }
            if($('#created_at').val()){
                params.created_at = $('#created_at').val()
            }
            params.post_title = $('#spost_title').val();
            return JSON.stringify(params)
        }

        function initTable() {
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
                    common.BTRowFormatAlignLeft('position_name', '岗位名称'),
                    common.BTRowFormatAlignLeft('department_name', '部门'),
                    common.BTRowFormatAlignLeft('username', '指派人'),
                    common.BTRowFormat('create_date', '发布时间'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'hr_state', '状态', 'stateInfo'),
                    common.BTRowFormatWithFormatterAlignLeft('hr_remark', '备注',common.remarkFormatter)
                ],
                sortOrder:'asc',
                idField: 'hr_id',
                uniqueId: 'hr_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'hr_id', $table)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            $('#domain_select').on('change', function(evt) {
                _self.queryConfirm()
            });
//            common.initSelect2($('#post_usergroup_id'), _self.pagePara.roleList)
            common.initSelect2($('#hr_checker_id'), _self.pagePara.staffInfo)
            let user_id = _self.pagePara.userInfo.user_id
//            common.initSelect2($('#post_title'), _self.pagePara.roleList2)
            initTable();
            common.initDatepicker($('#created_at'));
            $('#formAH').parsley()
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        addA: function(event) {
            let _self = this
            _self.workRow = {};
            $('#post_title').val(null).trigger('change')
            $('#post_usergroup_id').val(null).trigger('change')
            $('#hr_checker_id').val(null).trigger('change')
//            $('#post_title').val(null).trigger('change')
            $('#modAct').hide()
            $('#addAct').show()
            $('#formAH').parsley().reset()
            $('#AddHumanModal').modal('show')
        },
        submitA: async function(event) {
            let _self = this
            if ($('#formAH').parsley().isValid()) {
                try {
                    _self.workRow.department_id = _self.pagePara2.userDepartmentId
                    _self.workRow.position_id = _self.pagePara2.userPositionId
                    _self.workRow.hr_checker_id = common.getSelect2Val('hr_checker_id')
//                    _self.workRow.post_title = common.getSelect2Val('post_title')

                    if (_self.modFlag === '1') {
                        this.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                            let retData = response.data.info;
                            _self.pagePara2 = {};
                            $('#humanTable').bootstrapTable("refresh");
                            common.dealSuccessCommon('保存成功');
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        });
                    } //else if (_self.modFlag === '2') {
//                        common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'hr_id', $('#humanTable'))
                    //}
                    console.log('success')
                    $('#AddHumanModal').modal('hide')
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
            $('#humanTable').bootstrapTable("refresh")
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

                    $("#post_title").val(_self.pagePara2.userPositionName)
                    $("#post_usergroup_id").val(_self.pagePara2.userDepartmentName)
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
