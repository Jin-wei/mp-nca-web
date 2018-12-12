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
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">试题详情</h4>
                </div>

                <div class="panel-body">
                    <table id="documentDetailTable"></table>
                </div>

            </div>
        </div>
    </div>
</div>
</template>
<script>
import Vue from 'vue'
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
            oldRow: ''
        }
    },
    name: 'NCATestTaskInDetailControl',
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

            initAllDemandTable();
            common.reSizeCall();
        }

        function queryParams(params) {
            params.docdetail_id = _self.$route.query.docdetail_id;
            return JSON.stringify(params)
        }

        function initAllDemandTable() {

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('question_title', '掌握程度试题'),
                common.BTRowFormatAlignLeft('question_a', '选项A'),
                common.BTRowFormatAlignLeft('question_b', '选项B'),
                common.BTRowFormatAlignLeft('question_c', '选项C'),
                common.BTRowFormatAlignLeft('question_d', '选项D'),
                common.BTRowFormatEdSelect2Disabled(_self, 'question_answer', '答案', 'noticeAnswer')
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
    }
}
</script>
<style scoped>
</style>
