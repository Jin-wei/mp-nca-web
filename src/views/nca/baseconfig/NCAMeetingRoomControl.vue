<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">行政办公管理</a></li>
            <li class="active">会议室数据列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">会议室数据列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索会议室编号、会议室名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="meetingRoomTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddmeetingRoomModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增会议室</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formMR">
                        <div class="modal-body">
                            <div class="form-horizontal">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>会议室名称</label>
                                    <div class="col-md-9">
                                        <input class="form-control" v-model="workRow.meetingroom_name" id="meetingroom_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group">
                                    <label class="col-md-2 control-label" style="padding-left: 2px"><span class="table-required">*</span>会议室管理员</label>
                                    <div class="col-md-8" style="padding-left: 15px">
                                        <input class="form-control" id="meetinguser_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                                <div class="form-group">
                                    <label class="col-md-2 control-label" style="padding-left: 2px;"><span class="table-required">*</span>设备管理员</label>
                                    <div class="col-md-8" style="padding-left: 15px">
                                        <input class="form-control" id="equipmentuser_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>
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

        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMeetingRoomManageControl?method=';

import userSelectDialog from '../../../components/common/userSelectDialog.vue'
export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            workRow:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            mulityFlag: '',
            index: '',
            meetinguser_id: '',
            equipmentuser_id: ''
        }
    },
    name: 'NCAMeetingRoomControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $meetingRoomTable = $('#meetingRoomTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>',
                '<a class="btn btn-info btn-xs m-r-5 meeting_delete">删除</a>'
            ].join('')
        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAMeetingRoomDetailControl',
                    query: row
                })

            },
            'click .meeting_delete': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_r', $meetingRoomTable, row, 'meetingroom_id', function() {})
            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('meetingroom_id', '会议室编号'),
                common.BTRowFormatAlignLeft('meetingroom_name', '会议室名称'),
                common.BTRowFormatAlignLeft('meetinguser_name', '会议室管理员'),
                common.BTRowFormatAlignLeft('equipmentuser_name', '设备管理员'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $meetingRoomTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_r',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'meetingroom_id',
                uniqueId: 'meetingroom_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'meetingroom_id', $meetingRoomTable)
                }
            });
            common.changeTableClass($meetingRoomTable)
        }

        async function initData() {

            let entry_date = $('#entry_date').val()
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            let user_id = _self.pagePara.userInfo.user_id

            initFitOrderTable();
            common.reSizeCall();
            $('#formMR').parsley()
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#meetingRoomTable').bootstrapTable("refresh")
        },
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#meetinguser_id').val(null).trigger('change');
            $('#equipmentuser_id').val(null).trigger('change');
            $('#AddmeetingRoomModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formMR').parsley().isValid()) {
                try {
                    _self.workRow.meetinguser_id = _self.meetinguser_id;
                    _self.workRow.equipmentuser_id = _self.equipmentuser_id;

                    let response = await _self.$http.post(apiUrl + 'add_r', _self.workRow);
                    $('#meetingRoomTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formMR').parsley().reset();
                    $('#AddmeetingRoomModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(index) {
            let _self = this;
            _self.index = index;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback: async function(users) {
            let _self = this;
            let params =  {
                user_id: _self.pagePara.userInfo.user_id,
                users: users
            }
            try {
                _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                if (_self.index == 1){
                    $("#meetinguser_id").val(_self.pagePara2.meetingName)
                    _self.meetinguser_id = _self.pagePara2.meetingId
                } else {
                    $("#equipmentuser_id").val(_self.pagePara2.meetingName)
                    _self.equipmentuser_id = _self.pagePara2.meetingId
                }
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
