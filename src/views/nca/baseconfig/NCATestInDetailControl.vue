<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">内部文件</a></li>
        <li class="active">试题详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-12">
                            <div class="input-group ">
                                <span class="input-group-addon">文件条款</span>
                                <div>
                                    <input class="form-control" v-model="clause_title" readonly>
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
                        <button style="position: absolute;right:30px;" class="btn btn-info btn-xs" v-show="document_state ==0||document_state==2" @click="addDMDC" id="addDMDC">增加</button>
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">试题详情</h4>
                </div>

                <div class="panel-body">
                    <table id="documentDetailTable"></table>
                </div>

                <div class="modal fade" id="AdddDocumentMDModal">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">信息</h4>
                            </div>
                            <form @submit.prevent="submitForm" id="formDMDC">
                                <div class="modal-body">
                                    <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label" style="width:102px;"><span class="table-required">*</span>掌握程度试题</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_title" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>选项A</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_a" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>选项B</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_b" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>选项C</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_c" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>选项D</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.question_d" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>答案</label>
                                            <div class="col-md-9">
                                                <select class="form-control select2" style="width:100%" id="question_answer" data-parsley-required="true"></select>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="modal-footer">
                                    <button type="submit" class="btn btn-info" id="addAct">保存</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentManagementControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            document_id:'',
            clause_title:'',
            docdetail_id:'',
            oldRow: '',
            document_state: ''
        }
    },
    name: 'NCATestInDetailControl',
    mounted: function() {
        let _self = this;
        let $documentDetailTable = $('#documentDetailTable');
        let $fileTable = $('#documentFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            _self.docdetail_id =_self.$route.query.docdetail_id;
            _self.clause_title =_self.$route.query.clause_title;
            common.initSelect2($('#question_answer'), _self.pagePara.noticeAnswer)

            _self.document_id = common.getUrlParams('document_id');
            response = await _self.$http.post(apiUrl + 'search_d', {document_id:_self.document_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.document_state = _self.apply.document_state
            $('#document_state').val(_self.apply.document_state).trigger('change');

            initAllDemandTable();
            common.reSizeCall();
        }

        function queryParams(params) {
            params.docdetail_id = _self.$route.query.docdetail_id;
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 test_delete">删除</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .test_delete': function (e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_q', $documentDetailTable, row, 'docdetailquestion_id', function() {})
                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEdTextareaWidthAlignLeft('question_title', '掌握程度试题',250,17),
                common.BTRowFormatEditable('question_a', '选项A'),
                common.BTRowFormatEditable('question_b', '选项B'),
                common.BTRowFormatEditable('question_c', '选项C'),
                common.BTRowFormatEditable('question_d', '选项D'),
                common.BTRowFormatEdSelect2(_self, 'question_answer', '答案', 'noticeAnswer'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $documentDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_q',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
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
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_q', row, 'docdetailquestion_id', $documentDetailTable)
                }
            });
            common.changeTableClass($documentDetailTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        addDMDC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#question_answer').val(null).trigger('change')
            $('#AdddDocumentMDModal').modal('show');
        },
        submitForm: async function(event) {
            let _self = this;
            if ($('#formDMDC').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.document_id = _self.$route.query.document_id;
                    _self.workRow.question_answer = common.getSelect2Val('question_answer');
                    _self.workRow.docdetail_id =_self.$route.query.docdetail_id;
                    _self.workRow.type = 1;
                    let response = await _self.$http.post(apiUrl + 'add_q', _self.workRow)
                    let retData = response.data.info;
                    $('#documentDetailTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    });
                    console.log('success')
                    $('#documentDetailTable').bootstrapTable("refresh")
                    $('#AdddDocumentMDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
}
</script>
<style scoped>
</style>
