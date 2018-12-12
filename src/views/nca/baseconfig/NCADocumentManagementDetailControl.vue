<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">文控管理</a></li>
        <li class="active">内部文件</li>
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
                                    <input id='designUpload' type="file" name="file">
                                </label>
                            </div>
                        </div>
                        <button style="position: absolute;right:70px;"  id="saveApply" class="btn btn-info btn-xs" v-show="document_state ==0||document_state==2" v-on:click="saveDocument">提交</button>
                        <button style="position: absolute;right:30px;" class="btn btn-info btn-xs" @click="addDMDC" v-show="document_state ==0 || document_state ==2" id="addDMDC">增加</button>
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">文件详情</h4>
                </div>

                <div class="panel-body">
                    <table id="documentDetailTable"></table>
                </div>

                <div class="modal fade" id="AdddDocumentMDModal">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">文件列表</h4>
                            </div>
                            <form @submit.prevent="submitForm" id="formDMDC">
                                <div class="modal-body">
                                    <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>条款编号</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.clause_no" data-parsley-required="true" maxlength="40" data-parsley-maxlength="40">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>具体规定</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.clause_title" data-parsley-required="true" maxlength="1000" data-parsley-maxlength="1000">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>对应责任人</label>
                                            <div class="col-md-8" >
                                                <input class="form-control" id="user_ids" data-parsley-required="true" disabled>
                                            </div>
                                            <div class="col-md-1 padding-top-7">
                                                <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP">选择</label>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="modal-footer">
                                    <button type="submit" class="btn btn-info" @click="modFlag='1'" id="addAct">提交</button>
                                    <button type="submit" class="btn btn-info" @click="modFlag='2'" id="modAct">修改</button>
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
                    <table id="documentFilesTable"></table>
                </div>
            </div>
        </div>
    </div>

    <userSelectDialog :usergroupId="usergroupId" :users="users"></userSelectDialog>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentManagementControl?method=';

import userSelectDialog from '../../../components/common/userSelectDialog.vue';
import fileupload from '../../../components/common/fileUpload.vue';

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
            pagePara2: {},
            document_id:'',
            apply:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            modFlag: '',
            user_ids: '',
            document_state: '',
            upload: [],
            files: [],
            sFiles: {}
        }
    },
    name: 'NCADocumentManagementDetailControl',
    components: {
        userSelectDialog,
        fileupload
    },
    mounted: function() {
        let _self = this;
        let $documentDetailTable = $('#documentDetailTable');
        let $fileTable = $('#documentFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            let user_id = _self.pagePara.userInfo.user_id
            common.initSelect2($('#document_state'), _self.pagePara.stateType);

            _self.document_id = common.getUrlParams('document_id');
            response = await _self.$http.post(apiUrl + 'search_d', {document_id:_self.document_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.document_state = _self.apply.document_state;
            $('#document_state').val(_self.apply.document_state).trigger('change');

            common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
                _self.upload.push(fileInfo.name)
                let convert = {}
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                convert.document_id = common.getUrlParams('document_id');
                _self.files.push(convert)
                _self.$http.post(apiUrl + 'add_fi', convert).then((response) => {
                    let retData = response.data.info;
                    common.dealSuccessCommon('文件上传成功')
                    $('#documentFilesTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                async function getTablesData() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_files', {
                            document_id: common.getUrlParams('document_id')
                        });
                        let retdata = response.data.info;
                        $('#documentFilesTable').bootstrapTable('load', {
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
                    let response = await _self.$http.post(apiUrl + 'search_files', {
                        document_id: common.getUrlParams('document_id')
                    });
                    let retdata = response.data.info;
                    $('#documentFilesTable').bootstrapTable('load', {
                        data: retdata.designs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            initAllDemandTable();
            initFileTable();
            getTablesData(_self);
            common.reSizeCall();
        }

        function queryParams(params) {
            params.search_text=$('#search_text').val();
            params.document_id=common.getUrlParams('document_id');
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑</a>',
                '<a class="btn btn-info btn-xs m-r-5 show_detail">编辑试题</a>',
                '<a class="btn btn-info btn-xs m-r-5 meeting_delete">删除</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)

                    $('#user_ids').val(_self.workRow.user_ids).trigger('change')
                    $('#modAct').show()
                    $('#addAct').hide()
                    $('#formDMDC').parsley().reset()
                    $('#AdddDocumentMDModal').modal('show')
                },
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCATestInDetailControl',
                        query: row
                    })
                },
                'click .meeting_delete': function (e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_detail', $documentDetailTable, row, 'docdetail_id', function() {})
                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFormatter('clause_no', '条款编号',common.remarkFormatter),
                common.BTRowFormatWithFormatterAlignLeft('clause_title', '条款',common.remarkFormatter),
                common.BTRowFormatAlignLeft('user_ids', '对应责任人'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $documentDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_detail',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: 200,
                columns: columsArr,
                idField: 'docdetail_id',
                uniqueId: 'docdetail_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($documentDetailTable)
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
        addDMDC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#user_ids').val(null).trigger('change');
            $('#modAct').hide();
            $('#addAct').show();
            $('#AdddDocumentMDModal').modal('show');
        },
        saveDocument:function(event){
            let _self = this;
            let params={
                document_id:_self.document_id,
                type: 1
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
        submitForm: async function(event) {
            let _self = this;
            if ($('#formDMDC').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.document_id = _self.document_id;
                    _self.workRow.user_ids = _self.pagePara2.userId;

                    if (_self.modFlag === '1') {
                        let response = await _self.$http.post(apiUrl + 'add_detail', _self.workRow)
                        let retData = response.data.info;
                        $('#documentDetailTable').bootstrapTable('insertRow', {
                            index: 0,
                            row: retData
                        });
                    } else if (_self.modFlag === '2') {
                        common.rowModifyWithT(_self, apiUrl + 'modify_detail', _self.workRow, 'docdetail_id', $('#documentDetailTable'))
                    }
                    console.log('success')
                    $('#documentDetailTable').bootstrapTable("refresh")
                    $('#AdddDocumentMDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(index) {
            let _self = this;
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
                    $("#user_ids").val(_self.pagePara2.taskName)
                    _self.user_ids = _self.pagePara2.userId
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
