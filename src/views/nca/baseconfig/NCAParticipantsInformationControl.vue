<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button class="btn btn-info btn-xs" v-show="meeting_state !=1 " @click="addPIC" id="addPIC">新增人员</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">参会人员详情</h4>
                    </div>

                    <div class="panel-body">
                        <table id="participantsTable"></table>
                    </div>

                    <div class="modal fade" id="participantsModal">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">新增人员</h4>
                                </div>
                                <form @submit.prevent="submitForm" id="formPIC">
                                    <div class="modal-body row">
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>部门</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="p_usergroup_id"  data-parsley-required="true" disabled>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>角色</label>
                                            <div class="col-sm-8">
                                                <input type="text" class="form-control" id="usergroup_id"  data-parsley-required="true" disabled>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>姓名</label>
                                            <div class="col-sm-6">
                                                <input class="form-control" id="name"  data-parsley-required="true" disabled>
                                            </div>
                                            <div class="col-sm-2 padding-top-7">
                                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>工号</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" id="user_id"  data-parsley-required="true" disabled>
                                            </div>
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
    props: ['meeting', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara2:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            meeting_state: ''
        }
    },
    name: 'NCAParticipantsInformationControl',
    components: {
        userSelectDialog
    },
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            let $participantsTable = $('#participantsTable');
            _self.meeting_state = common.getUrlParams('meeting_state');
            common.initSelect2($('#meetingattendee_state'), this.pagePara.meetingroomInfo);
            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 accept_appointment">删除</a>'
                ].join('')

            }
            window.tableEvents = {
                'click .accept_appointment': function (e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_a', $participantsTable, row, 'meetingattendee_id', function() {})
                }
            };
            function queryParams(params) {
//                common.initImageViewer()
                params.meeting_id =_self.meeting.meeting_id
                return JSON.stringify(params);
            }

            function initTable() {
                $participantsTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_a',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('attendee_id', '工号'),
                        common.BTRowFormatAlignLeft('name', '姓名'),
                        common.BTRowFormatAlignLeft('p_usergroup_name', '部门'),
                        common.BTRowFormatAlignLeft('usergroup_name', '岗位'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'meetingattendee_state', '状态', 'meetingroomInfo'),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    idField: 'meetingattendee_id',
                    uniqueId: 'meetingattendee_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($participantsTable)
            }
            $(function() {
                initTable();
            })
        }
    },
    methods: {
        addPIC: function(event) {
            let _self = this;
            _self.workRow = {};
            window.setTimeout(function () {
                _self.$http.post(apiUrl + 'search_ds', {
                    meeting_id: _self.meeting.meeting_id
                }).then((response)=>{
                    let retData = response.data.info;
                    _self.meeting_state = retData;
                    if (_self.meeting_state == 1){
                        $('#participantsModal').modal('hide');
                        common.dealSuccessCommon('已提交');
                    }
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
            },500);

            $('#p_usergroup_id').val(null).trigger('change')
            $('#usergroup_id').val(null).trigger('change')
            $('#name').val(null).trigger('change')
            $('#user_id').val(null).trigger('change')
            $('#formPIC').parsley().reset()
            $('#participantsModal').modal('show')
        },
        submitForm: async function(event) {
            let _self = this;
            let formPIC = $('#formPIC')
            if (formPIC.parsley().isValid()) {
                _self.workRow.meeting_id = _self.meeting.meeting_id;
                _self.workRow.attendee_id = _self.pagePara2.meetingId;

                _self.$http.post(apiUrl + 'add_a', _self.workRow).then((response)=>{
                    let retData = response.data.info;
                    $('#participantsTable').bootstrapTable("refresh");
                    $('#participantsModal').modal('hide');
                    _self.workRow = {};
                    common.dealSuccessCommon('已增加');
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
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

                    $("#p_usergroup_id").val(_self.pagePara2.groupName)
                    $("#usergroup_id").val(_self.pagePara2.userGroupName)
                    $("#name").val(_self.pagePara2.meetingName)
                    $("#user_id").val(_self.pagePara2.meetingId)
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
