<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">外部文件</a></li>
        <li class="active">试题详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">文件编号</span>
                                <div>
                                    <input class="form-control" v-model="apply.document_id" readonly>
                                </div>
                            </div>
                        </div>

                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">文件题目</span>
                                <div>
                                    <input class="form-control" v-model="apply.document_title" readonly>
                                </div>
                            </div>
                        </div>

                        <div class="form-group col-sm-2">
                            <div class="input-group ">
                                <span class="input-group-addon">状态</span>
                                <div>
                                    <select class="form-control select2" id="document_state" disabled></select>
                                </div>
                            </div>
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
                    <div class="panel-heading-btn" style="position:relative">
                        <div class="form-group" style="position: absolute;right:110px;">
                            <div v-show="document_state ==0||document_state==2">
                                <label class="btn btn-info btn-xs fileupload-button">上传附件
                                    <input id='testUpload' type="file" name="file">
                                </label>
                            </div>
                        </div>
                        <button style="position: absolute;right:70px;"  id="saveApply" class="btn btn-info btn-xs" v-show="document_state ==0||document_state==2" v-on:click="saveDocument">提交</button>
                        <button class="btn btn-info btn-xs" v-show="document_state ==0||document_state==2" @click="addU">增加</button>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">发布对象</h4>
                </div>
                <div class="panel-body">
                    <table id="testOutUserTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn" style="position:relative">
                        <button style="position: absolute;right:30px;" class="btn btn-info btn-xs" @click="addTODC" v-show="document_state ==0||document_state==2" id="addTODC">增加</button>
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">试题详情</h4>
                </div>

                <div class="panel-body">
                    <table id="TestOutNoticeTable"></table>
                </div>

                <div class="modal fade" id="AddTestOutMDModal">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">信息</h4>
                            </div>
                            <form @submit.prevent="submitForm" id="formTODC">
                                <div class="modal-body">
                                    <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label" style="width: 102px;">掌握程度试题</label>
                                            <div class="col-md-9" style="padding: 0px 21px 0px 9px">
                                                <input class="form-control" v-model="workRow.question_title" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">选项A</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_a" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">选项B</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_b" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">选项C</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_c" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">选项D</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_d" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label">答案</label>
                                            <div class="col-md-9">
                                                <select class="form-control select2" style="width:100%" id="question_answer" data-parsley-required="true"></select>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="modal-footer">
                                    <button type="submit" class="btn btn-info" id="addAct"><i class="fa fa-fw fa-plus"></i>增加</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="addUserModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">增加</h4>
                </div>
                <form @submit.prevent="addUserAct" id="userForm">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">部门</label>
                                <div class="col-sm-8">
                                    <input class="form-control" name='p_usergroup_id' id="p_usergroup_id" data-parsley-required="true" disabled>
                                    <!--<select class="form-control select2" style="width:100%" id="p_usergroup_id" data-parsley-required="true" ></select>-->
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <label class="col-sm-4 control-label">岗位</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="usergroup_id" data-parsley-required="true" disabled>
                                    <!--<select class="form-control select2" style="width:100%" id="usergroup_id" data-parsley-required="true" ></select>-->
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info"><i class="fa fa-fw fa-plus"></i>增加</button>
                    </div>
                </form>
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
                    <table id="documentOutFilesTable"></table>
                </div>
            </div>
        </div>
    </div>

    <userGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userGroup>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentManagementControl?method=';

import fileupload from '../../../components/common/fileUpload.vue';
import userGroup from './userGroup.vue';

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
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            pagePara2:{},
            document_id:'',
            clause_title:'',
            docdetail_id:'',
            oldRow: '',
            apply:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            document_state: '',
            upload: [],
            files: [],
            sFiles: {}
        }
    },
    name: 'NCATestOutDetailControl',
    components: {
        userGroup,
        fileupload
    },
    mounted: function() {
        let _self = this;
        let $TestOutNoticeTable = $('#TestOutNoticeTable');
        let $testOutUserTable = $('#testOutUserTable');
        let $fileTable = $('#documentOutFilesTable');

        _self.purchaseapplyId = _self.$route.query.purchaseapplyId;
        _self.order_type = _self.$route.query.order_type;

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#document_state'), _self.pagePara.stateType);
            common.initSelect2($('#question_answer'), _self.pagePara.noticeAnswer);
            _self.document_id = _self.$route.query.document_id;
            _self.docdetail_id =_self.$route.query.docdetail_id;
            _self.clause_title =_self.$route.query.clause_title;
            let user_id = _self.pagePara.userInfo.user_id;

            _self.document_id = common.getUrlParams('document_id');
            response = await _self.$http.post(apiUrl + 'search_d', {document_id:_self.document_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.document_state = _self.apply.document_state;
            $('#document_state').val(_self.apply.document_state).trigger('change');

            common.fileUpload(_self, $('#testUpload'), apiUrl, function(fileInfo) {
                _self.upload.push(fileInfo.name)
                let convert = {}
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                convert.document_id = common.getUrlParams('document_id');
                _self.files.push(convert)
                _self.$http.post(apiUrl + 'add_ofi', convert).then((response) => {
                    let retData = response.data.info;
                    common.dealSuccessCommon('文件上传成功')
                    $('#documentOutFilesTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                async function getTablesData() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_ofiles', {
                            document_id: common.getUrlParams('document_id')
                        });
                        let retdata = response.data.info;
                        $('#documentOutFilesTable').bootstrapTable('load', {
                            data: retdata.designs
                        });
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
                getTablesData()
                console.log('success')
            });

            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_ofiles', {
                        document_id: common.getUrlParams('document_id')
                    });
                    let retdata = response.data.info;
                    $('#documentOutFilesTable').bootstrapTable('load', {
                        data: retdata.designs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            initAllDemandTable();
            initTable();
            initFileTable();
            getTablesData(_self);
            common.reSizeCall();
        }

        function queryParams(params) {
            params.document_id = _self.$route.query.document_id;
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 test_delete btn-info-table">删除</a>'
            ].join('')
        }
        function operateFormatterU(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 user_delete btn-info-table">删除</a>'
            ].join('')
        }

        window.tableEvents = {
            'click .test_delete': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_q', $TestOutNoticeTable, row, 'docdetailquestion_id', function() {})
            },
            'click .user_delete': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_ug', $testOutUserTable, row, 'docusergroup_id', function() {})
            }

        };

        function initAllDemandTable() {

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEditableLeft('question_title', '掌握程度试题'),
                common.BTRowFormatEditable('question_a', '选项A'),
                common.BTRowFormatEditable('question_b', '选项B'),
                common.BTRowFormatEditable('question_c', '选项C'),
                common.BTRowFormatEditable('question_d', '选项D'),
                common.BTRowFormatEdSelect2(_self, 'question_answer', '答案', 'noticeAnswer'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $TestOutNoticeTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_q',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: 200,
                columns: columsArr,
                idField: 'docdetailquestion_id',
                uniqueId: 'docdetailquestion_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_q', row, 'docdetailquestion_id', $TestOutNoticeTable)
                }
            });
            common.changeTableClass($TestOutNoticeTable)
        }



        function queryParamsU(params) {
            params.document_id = _self.$route.query.document_id;
            return JSON.stringify(params);
        }

        function initTable() {
            $testOutUserTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_ug',
                queryParams: queryParamsU,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    _self.warehouseZones = res.info.rows
                    return res.info;
                },
                height: 200,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('p_usergroup_name', '部门'),
                    common.BTRowFormatAlignLeft('usergroup_name', '岗位'),
                    common.BTRowFormatWithFE('', '操作', operateFormatterU, tableEvents)
                ],
                idField: 'docusergroup_id',
                uniqueId: 'docusergroup_id',
                striped: true,
                clickToSelect: true,
                pagination: false,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_zone', row, 'docusergroup_id', $testOutUserTable)
                }
            });
            common.changeTableClass($testOutUserTable)
        }

        function initFileTable() {
            let columns;
            columns = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('document_id', '文件编号'),
                common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
            ]

            $fileTable.bootstrapTable('destroy');

            $fileTable.bootstrapTable({
                columns: columns,
                idField: 'document_id',
                uniqueId: 'document_id',
                clickToSelect: true,
                height: 200,
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($fileTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        addTODC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#question_answer').val(null).trigger('change')
            $('#AddTestOutMDModal').modal('show');

        },
        submitForm: async function(event) {
            let _self = this;
            if ($('#formTODC').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.document_id = _self.$route.query.document_id;
                    _self.workRow.question_answer = common.getSelect2Val('question_answer');
                    _self.workRow.docdetail_id = 0;
                    _self.workRow.type = 2;
                    let response = await _self.$http.post(apiUrl + 'add_q', _self.workRow)
                    let retData = response.data.info;
                    $('#TestOutNoticeTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    });
                    console.log('success')
                    $('#TestOutNoticeTable').bootstrapTable("refresh")
                    $('#AddTestOutMDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        addU: function () {
            let _self = this;
            _self.workRow = {}
            $('#p_usergroup_id').val(null).trigger('change')
            $('#usergroup_id').val(null).trigger('change')
            $('#addUserModal').modal('show')

        },
        addUserAct: function (event) {
            let _self = this;
            if ($('#userForm').parsley().isValid()){
                _self.workRow.document_id = _self.$route.query.document_id;
                _self.workRow.p_usergroup_id= _self.pagePara2.groupId;
                _self.workRow.usergroup_id = _self.pagePara2.userGroupId;

                _self.$http.post(apiUrl + 'add_ug', _self.workRow).then((response) => {
                    let retData = response.data.info;
                    $('#addUserModal').modal('hide')
                    $('#testOutUserTable').bootstrapTable("refresh", _self.queryParams);
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            }
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback2: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeUser', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#p_usergroup_id").val(_self.pagePara2.groupName)
                    $("#usergroup_id").val(_self.pagePara2.userGroupName)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        saveDocument:function(event){
            let _self = this;
            let params={
                document_id:_self.document_id,
                type: 2
            }
            _self.$http.post(apiUrl + 'submit', params).then((response) => {
                let retData = response.data.info;
                this.document_state = retData.document_state;

                $('#document_state').val(this.document_state).trigger('change');
                common.dealSuccessCommon('提交成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
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
