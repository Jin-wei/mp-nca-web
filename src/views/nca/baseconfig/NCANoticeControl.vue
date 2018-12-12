<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">公告管理</a></li>
        <li class="active">公告列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">公告列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="created_at" placeholder="开始时间"/>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索公告名称" id="notice_title" style="width: 180px">
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
                    <table id="noticeTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增公告列表</h4>
                </div>
                <form @submit.prevent="submitA" id="formA">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>名称</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.notice_title" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>公告范围</label>
                                <div class="col-md-9">
                                    <select class="form-control select2" style="width:100%" id="orgs" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">详情</label>
                                <div class="col-md-9">
                                    <textarea class="form-control" v-model="workRow.notice_detail" maxlength="200" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">问题</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.notice_question" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">A</label>
                                <div class="col-md-4">
                                    <input class="form-control" v-model="workRow.notice_answera" maxlength="50" data-parsley-maxlength="50">
                                </div>
                                <label class="col-md-1 control-label">B</label>
                                <div class="col-md-4">
                                    <input class="form-control" v-model="workRow.notice_answerb" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label">C</label>
                                <div class="col-md-4">
                                    <input class="form-control" v-model="workRow.notice_answerc" maxlength="50" data-parsley-maxlength="50">
                                </div>
                                <label class="col-md-1 control-label">D</label>
                                <div class="col-md-4">
                                    <input class="form-control" v-model="workRow.notice_answerd" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>答案</label>
                                <div class="col-md-9">
                                    <select class="form-control select2" style="width:100%" id="notice_answer" data-parsley-required="true"></select>
                                    <!--<input class="form-control" v-model="workRow.notice_answer" maxlength="50" data-parsley-maxlength="50">-->
                                </div>
                            </div>
                            <div class="form-group">
                                <div style="margin-left: 40px">
                                    <label class="btn btn-info btn-xs fileupload-button">上传附件
                                        <input id='designUpload' type="file" name="file">
                                    </label>
                                </div>
                            </div>
                            <div class="form-group">
                                <div style="margin-left: 30px">
                                    <div class="image-content" v-for="(name, index) in upload">
                                        <h7>{{name}}</h7>
                                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-danger delete-btn" @click="removImage(index)">
                                            <i class="fa fa-times"></i>
                                        </a>
                                    </div>
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
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCANoticeControl?method=';
import fileupload from '../../../components/common/fileUpload.vue'

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
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            modFlag: '',
            orgs2: [],
            upload: [],
            files: []
        }
    },
    components: {fileupload},
    name: 'NCANoticeControl',
    mounted: async function() {
        let _self = this;
        let $table = $('#noticeTable');

        function queryParams(params) {
            let domain_id = common.getSelect2Val('domain_select')
            if (domain_id) {
                params.domain_id = domain_id
            }
            if($('#created_at').val()){
                params.created_at = $('#created_at').val()
            }
            params.notice_title = $('#notice_title').val();
            return JSON.stringify(params)
        }

        function editFormatter(value, row) {
            if (row.notice_state==='2') {
            return [
                '<a class="btn btn-xs btn-info m-r-5 tableDelete">删除</a>' +
                '<a class="btn btn-xs btn-info m-r-5 noticedetail">查看</a>'
            ].join('')
            } else if (row.notice_state==='1' || row.notice_state==='3') {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 noticedetail">查看</a>'
                ].join('')
            }
        }

        function initTable() {
            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $table, row, 'notice_id', function() {})
                },
                'click .noticedetail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCANoticeDetailControl',
                        query: {
                            notice_id: row.notice_id
                        }
                    })
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
                    common.BTRowFormatAlignLeft('notice_title', '公告名称'),
                    common.BTRowFormatAlignLeft('username', '发布人'),
                    common.BTRowFormat('create_date', '发布时间'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'notice_state', '状态', 'stateInfo'),
                    common.BTRowFormatWithFormatterAlignLeft('notice_refuse_remark', '驳回说明',common.remarkFormatter),
                    common.actFormatter('edit', editFormatter, tableEvents),
                ],
                sortOrder:'asc',
                idField: 'notice_id',
                uniqueId: 'notice_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'notice_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            $('#domain_select').on('change', function(evt) {
                _self.queryConfirm()
            });
            common.initSelect2($('#orgs'), _self.pagePara.roleList)
            common.initSelect2($('#notice_answer'), _self.pagePara.noticeAnswer)
            let multiple = $('#orgs').select2({//多选
                allowClear: true
            });
            initTable();

            common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
            _self.upload.push(fileInfo.name)
            let convert = {}
            convert.file_name = fileInfo.name;
            convert.file_url = fileInfo.url;
            convert.file_type = fileInfo.type;
            _self.files.push(convert)
            console.log('success')
        });

            common.initDatepicker($('#created_at'));
            $('#formA').parsley()
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        addA: function(event) {
            let _self = this
            _self.workRow = {}
            $('#orgs').val(null).trigger('change')
            $('#notice_answer').val(null).trigger('change')
            $('#modAct').hide()
            $('#addAct').show()
            $('#formA').parsley().reset()
            _self.upload = []
            _self.files = []
            $('#AddModal').modal('show')
        },
        submitA: async function(event) {
            if ($('#formA').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.orgs = common.getSelect2Val('orgs')
                    _self.workRow.notice_answer = common.getSelect2Val('notice_answer')
                    _self.workRow.files = _self.files
                    if (_self.workRow.orgs == '0'){
                        for (let i = 0; i < _self.pagePara.roleList.length; i++) {
                            if (i != 0 ) {
                                _self.orgs2.push(_self.pagePara.roleList[i].id)
                                _self.workRow.orgs = _self.orgs2
                            }
                        }
                    } else {
                        _self.workRow.orgs = $('#orgs').val()
                    }
                    if (_self.modFlag === '1') {
                        this.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                            let retData = response.data.info;
                            $('#noticeTable').bootstrapTable("refresh");
                            common.dealSuccessCommon('保存成功');
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        });
                    } //else if (_self.modFlag === '2') {
//                        common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'notice_id', $('#noticeTable'))
                    //}
                    console.log('success')
                    $('#AddModal').modal('hide')
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
            $('#noticeTable').bootstrapTable("refresh")
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
