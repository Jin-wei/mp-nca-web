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
                        <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">试题详情</h4>
                </div>

                <div class="panel-body">
                    <table id="TestOutNoticeTable"></table>
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
            oldRow: '',
            apply:{},
            document_state: '',
            files: [],
            show_return: 0,
            show_answer: 0
        }
    },
    name: 'NCATestTaskOutDetailControl',
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
            _self.show_return = _self.$route.query.show_return;
            _self.show_answer = _self.$route.query.show_answer;
            console.log('show_return',_self.show_return)
            let user_id = _self.pagePara.userInfo.user_id;

            _self.document_id = common.getUrlParams('document_id');
            response = await _self.$http.post(apiUrl + 'search_d', {document_id:_self.document_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.document_state = _self.apply.document_state;
            $('#document_state').val(_self.apply.document_state).trigger('change');

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

        function initAllDemandTable() {
            let columsArr = []
            console.log('_self.show_answer',_self.show_answer)
            if (_self.show_answer == 0 ) {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('question_title', '掌握程度试题'),
                    common.BTRowFormatAlignLeft('question_a', '选项A'),
                    common.BTRowFormatAlignLeft('question_b', '选项B'),
                    common.BTRowFormatAlignLeft('question_c', '选项C'),
                    common.BTRowFormatAlignLeft('question_d', '选项D'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'question_answer', '答案', 'noticeAnswer')
                ];
            } else {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('question_title', '掌握程度试题'),
                    common.BTRowFormatAlignLeft('question_a', '选项A'),
                    common.BTRowFormatAlignLeft('question_b', '选项B'),
                    common.BTRowFormatAlignLeft('question_c', '选项C'),
                    common.BTRowFormatAlignLeft('question_d', '选项D'),
                    // common.BTRowFormatEdSelect2Disabled(_self, 'question_answer', '答案', 'noticeAnswer')
                ];
            }
            $TestOutNoticeTable.bootstrapTable({
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
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('p_usergroup_name', '部门'),
                    common.BTRowFormatAlignLeft('usergroup_name', '岗位')
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
    }
}
</script>
<style scoped>
</style>
