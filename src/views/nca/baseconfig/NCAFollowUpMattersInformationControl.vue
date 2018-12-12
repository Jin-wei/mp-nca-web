<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn" style="position:relative">
                            <div class="form-group" style="position: absolute;right:70px;">
                                <div v-show="meeting_remark_state !=1 ">
                                    <label class="btn btn-info btn-xs fileupload-button">上传附件
                                        <input id='designUpload' type="file" name="file">
                                    </label>
                                </div>
                            </div>
                            <button style="position: absolute;right:20px;" class="btn btn-info btn-xs" v-show="meeting_remark_state !=1 " @click="addFUMIC" id="addFUMIC">增加</button>
                            <a style="position: absolute;right:-3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">跟进事项详情</h4>
                    </div>

                    <div class="panel-body">
                        <table id="followUpMattersTable"></table>
                    </div>

                    <div class="modal fade" id="followUpMattersModal">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">新增</h4>
                                </div>
                                <form @submit.prevent="submitForm" id="formFUMIC">
                                    <div class="modal-body">
                                        <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">跟进事项</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.follow_remark" id="follow_remark" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label" style="width: 110px;">实施责任人</label>
                                            <div class="col-md-7" style="padding-left: 0px">
                                                <input class="form-control" id="executor_id" data-parsley-required="true" disabled>
                                            </div>
                                            <div class="col-sm-2">
                                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label" style="width: 110px;">验收责任人</label>
                                            <div class="col-md-7" style="padding-left: 0px">
                                                <input class="form-control" id="checker_id" data-parsley-required="true" disabled>
                                            </div>
                                            <div class="col-sm-2">
                                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">完成时间</label>
                                            <div class="col-md-9">
                                                <input type="text" class="form-control" id="finish_date" data-parsley-required="true" >
                                            </div>
                                        </div>
                                    </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="submit" class="btn btn-info">保存</button>
                                    </div>
                                </form>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">附件信息</h4>
                    </div>
                    <div class="panel-body">
                        <table id="meetingFilesTable"></table>
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
const apiUrl = '/api/nca/baseconfig/NCAMeetingMinuteControl?method=';

import fileupload from '../../../components/common/fileUpload.vue'
import userSelectDialog from '../../../components/common/userSelectDialog.vue'

function generateUploadFile(_self, fileInfo) {
    if (_self) {
        let convert = {}
        convert.file_name = fileInfo.name;
        convert.file_url = fileInfo.url;
        convert.file_type = fileInfo.type;
        _self.files.push(convert)
        console.log('success')
    }
}
export default {
    props: ['meeting', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara2:{},
            meeting_remark_state: '',
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            mulityFlag: '',
            index: '',
            executor_id: '',
            checker_id: '',
            upload: [],
            files: [],
            sFiles: {}
        }
    },
    name: 'NCADemandEquipmentControl',
    components: {
        userSelectDialog,
        fileupload
    },
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            let $followUpMattersTable = $('#followUpMattersTable');
            let $fileTable = $('#meetingFilesTable');
            _self.meeting_remark_state = _self.meeting.meeting_remark_state
            let user_id = _self.meeting.user_id

            async function initData(){
            common.initDateTimepicker($('#finish_date'));
            let finish_date = $('#finish_date').val();
                let param = {
                    meeting_id: _self.meeting.meeting_id
                }
            let response = await _self.$http.post(apiUrl + 'search_f', param);
            let sFiles = response.data.info.sFiles;
            _self.sFiles = JSON.parse(JSON.stringify(sFiles));

            common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
                _self.upload.push(fileInfo.name)
                let convert = {}
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                convert.meeting_id = _self.meeting.meeting_id
                _self.files.push(convert)
                _self.$http.post(apiUrl + 'add_fi', convert).then((response) => {
                    let retData = response.data.info;
                    common.dealSuccessCommon('文件上传成功')
                    $('#meetingFilesTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
                async function getTablesData() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_files', {
                            meeting_id: _self.meeting.meeting_id
                        });
                        let retdata = response.data.info;
                        $('#meetingFilesTable').bootstrapTable('load', {
                            data: retdata.designs
                        });
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
                getTablesData()
                console.log('success')
            });

            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 accept_appointment">删除</a>'
                ].join('')

            }
            window.tableEvents = {
                'click .accept_appointment': function (e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_f', $followUpMattersTable, row, 'meetingfollow_id', function() {})
                }
            };
            function queryParams(params) {
//                common.initImageViewer()
                params.meeting_id =_self.meeting.meeting_id
                return JSON.stringify(params);
            }

            function initTable() {
                $followUpMattersTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_f',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('follow_remark', '跟进事项'),
                        common.BTRowFormatAlignLeft('executor_name', '实施责任人'),
                        common.BTRowFormatAlignLeft('checker_id_name', '验收责任人'),
                        common.BTRowFormat('finish_date', '完成时间'),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    idField: 'meetingfollow_id',
                    uniqueId: 'meetingfollow_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($followUpMattersTable)
            }

            function initFileTable() {
                let columns;
                columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('meeting_id', '会议ID'),
                    common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
                ]

                $fileTable.bootstrapTable('destroy');

                $fileTable.bootstrapTable({
                    columns: columns,
                    idField: 'meeting_id',
                    uniqueId: 'meeting_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($fileTable)
            }

            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_files', {
                        meeting_id: _self.meeting.meeting_id
                    });
                    let retdata = response.data.info;
                    $('#meetingFilesTable').bootstrapTable('load', {
                        data: retdata.designs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            $(function() {
                initData();
                initTable();
                initFileTable();
                getTablesData(_self);
            })
        }
    },
    methods: {
        addFUMIC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#finish_date').val(null).trigger('change');
            $('#executor_id').val(null).trigger('change');
            $('#checker_id').val(null).trigger('change');
            $('#formFUMIC').parsley().reset()
            $('#followUpMattersModal').modal('show')
        },
        submitForm: async function(event) {
            let _self = this;
            let formFUMIC = $('#formFUMIC')
            if (formFUMIC.parsley().isValid()) {
                _self.workRow.meeting_id = _self.meeting.meeting_id;
                _self.workRow.finish_date = $('#finish_date').val();
                _self.workRow.executor_id = _self.executor_id;
                _self.workRow.checker_id = _self.checker_id;

                _self.$http.post(apiUrl + 'add_f', _self.workRow).then((response)=>{
                    let retData = response.data.info;
                    $('#followUpMattersTable').bootstrapTable("refresh");
                    $('#followUpMattersModal').modal('hide');
                    _self.workRow = {};
                    common.dealSuccessCommon('已提交');
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
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
                user_id: _self.meeting.user_id,
                users: users
            }
            try {
                _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    if (_self.index == 1){
                        $("#executor_id").val(_self.pagePara2.meetingName)
                        _self.executor_id = _self.pagePara2.meetingId
                    } else {
                        $("#checker_id").val(_self.pagePara2.meetingName)
                        _self.checker_id = _self.pagePara2.meetingId
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        removImage: async function(index) {
            let _self = this
            _self.upload.splice(index, 1)
            _self.files.splice(index, 1)
        },
        //upload component callback
        startUpload(e) {
            // file upload start event
        },
        finishUpload(fileInfo) {
            // file upload finish event
            let _self = this
            generateUploadFile(_self, fileInfo)
        },
        failUpload(message) {
            common.dealWarningCommon(message)
        },
        progress(e) {
            // file upload progress
            // returns false if progress is not computable
        }
    }
}
</script>
<style scoped>
</style>
