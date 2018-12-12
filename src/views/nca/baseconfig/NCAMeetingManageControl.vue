NCAMeetingManageControl<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">行政办公管理</a></li>
            <li class="active">会议列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">会议列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索会议名称、会议地点" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="created_at" placeholder="时间" />
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addMMC" class="btn btn-info" v-on:click="addMMC">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="meetingManageTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddMeetingManageModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增会议列表</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formMMC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>会议议题</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.meeting_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>会议地点</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="meetingroom_id" data-parsley-required="true" ></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-right: 0px"><span class="table-required">*</span>会议起始时间</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="start_time" data-parsley-required="true" >
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-right: 0px"><span class="table-required">*</span>会议结束时间</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="end_time" data-parsley-required="true" >
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px"><span class="table-required">*</span>会议主持人</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input class="form-control" id="host_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">提交</button>
                        </div>
                        <!--<div id="textEditor" style="height: 300px;"><p>请输入内容...</p></div>-->
                    </form>
                </div>
            </div>
        </div>

        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMeetingManageControl?method=';

import userSelectDialog from '../../../components/common/userSelectDialog.vue'
export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            workRow:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue()
        }
    },
    name: 'NCAMeetingManageControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $meetingManageTable = $('#meetingManageTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>',
                '<a class="btn btn-info btn-xs m-r-5 delete_meeting">删除</a>'
            ].join('')

        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAMeetingManageDetailControl',
                    query: row
                })

            },
            'click .delete_meeting': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_m', $meetingManageTable, row, 'meeting_id', function() {})
            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            if ($('#created_at').val()) {
                params.start_time= $('#created_at').val();
            }
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('meeting_name', '会议议题'),
                common.BTRowFormatAlignLeft('host_name', '会议主持人'),
                common.BTRowFormat('start_time', '会议开始时间'),
                common.BTRowFormat('end_time', '会议结束时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'meetingroom_id', '会议地点', 'meetingroomList'),
                common.BTRowFormatEdSelect2Disabled(_self,'meetingroom_state', '会议室状态','meetingroomInfo'),
                common.BTRowFormatEdSelect2Disabled(_self,'host_state', '会议主持人状态','meetingroomInfo'),
                common.BTRowFormatEdSelect2Disabled(_self, 'meeting_state', '状态', 'stateInfo'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $meetingManageTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_m',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'meeting_id',
                uniqueId: 'meeting_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'meeting_id', $meetingManageTable)
                }
            });
            common.changeTableClass($meetingManageTable)
        }

        async function initData() {
            common.initDatepicker($('#created_at'));
            common.initDateTimepicker($('#start_time'));
            let start_time = $('#start_time').val()
            common.initDateTimepicker($('#end_time'));
            let end_time = $('#end_time').val()

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            let user_id = _self.pagePara.userInfo.user_id

            common.initSelect2($('#meetingroom_id'), retData.meetingroomList);
            common.initSelect2($('#meetingroom_state'), retData.meetingroomList);
            common.initSelect2($('#host_state'), retData.meetingroomList);
            common.initSelect2($('#meeting_state'), retData.stateInfo);

//            var E = window.wangEditor;
//            var editor = new E('#textEditor')
//            editor.create()

            initFitOrderTable();
            common.reSizeCall();
            $('#formMMC').parsley()
        }


        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#meetingManageTable').bootstrapTable("refresh")
        },
        addMMC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#meetingroom_id').val(null).trigger('change');
            $('#start_time').val(null).trigger('change');
            $('#end_time').val(null).trigger('change');
            $('#host_id').val(null).trigger('change');
            $('#AddMeetingManageModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formMMC').parsley().isValid()) {
                try {
                    _self.workRow.start_time = $('#start_time').val();
                    _self.workRow.end_time = $('#end_time').val();
                    _self.workRow.meetingroom_id = common.getSelect2Val('meetingroom_id');
                    _self.workRow.host_id = _self.pagePara2.meetingId;

                    let response = await _self.$http.post(apiUrl + 'add_m', _self.workRow);
                    $('#meetingManageTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formMMC').parsley().reset();
                    $('#AddMeetingManageModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeGroup', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#host_id").val(_self.pagePara2.meetingName)
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
