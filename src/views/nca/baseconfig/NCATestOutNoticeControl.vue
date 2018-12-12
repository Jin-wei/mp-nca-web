<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">文件通知管理</a></li>
        <li class="active">外部文件</li>
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
                                    <input class="form-control" v-model="document.document_id" readonly>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">文件题目</span>
                                <div>
                                    <input class="form-control" v-model="document.document_title" readonly>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-sm-2">
                            <div class="input-group ">
                                <span class="input-group-addon">状态</span>
                                <div>
                                    <select class="form-control select2" id="read_state" disabled></select>
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
                        <button style="position: absolute;right:30px;" class="btn btn-info btn-xs" v-show="read_state == 0" @click="addDMDC" id="addDMDC">提交</button>
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
                    <table id="testOutNoticeFilesTable"></table>
                </div>
            </div>
        </div>
    </div>

</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentNoticeControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            document_id:'',
            apply:{},
            document: {},
            docuserstate: {},
            read_state: '',
            docdetail_id: '',
            answerArray: []
        }
    },
    name: 'NCATestOutNoticeControl',
    mounted: function() {
        let _self = this;
        let $documentDetailTable = $('#documentDetailTable');
        let $fileTable = $('#testOutNoticeFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#read_state'), _self.pagePara.stateType);
            _self.answerArray = _self.pagePara.answerType;

            let params ={
                document_id:common.getUrlParams('document_id'),
                docdetail_id:null,
                user_id: _self.pagePara.userInfo.user_id
            }
            console.log('params',params)
            response = await _self.$http.post(apiUrl + 'search_d', params);
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.document =_self.apply.document;
            _self.docuser =_self.apply.docuser;
            if (_self.docuser) {
                _self.read_state = _self.docuser.read_state;
                $('#read_state').val(_self.docuser.read_state).trigger('change');
            }
            console.log('_self.apply',_self.apply)
            let retData = response.data.info;

            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_ofiles', {
                        document_id: common.getUrlParams('document_id')
                    });
                    let retdata = response.data.info;
                    $('#testOutNoticeFilesTable').bootstrapTable('load', {
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
            params.document_id=common.getUrlParams('document_id');
            return JSON.stringify(params)
        }

        function selectFormat(rowid, rowname, dataList, width = 200) {
            return {
                field: rowid,
                title: rowname,
                class: 'text-nowrap',
                align: 'center',
                valign: 'middle',
                editable: {
                    type: 'select2',
                    emptytext: '请选择答案',
                    select2: {
                        data: dataList,
                        width: width
                    },
                    display: function(value) {
                        let showText = '';
                        $(dataList).each(function() {
                            if (value && value !== 'undefined') {
                                if (this.id == value) {
                                    showText = this.text;
                                    return false
                                }
                            }
                        });
                        $(this).html(showText);
                    }
                }
            }
        }

        function initAllDemandTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('question_title', '掌握程度试题'),
                common.BTRowFormat('question_a', '选项A'),
                common.BTRowFormat('question_b', '选项B'),
                common.BTRowFormat('question_c', '选项C'),
                common.BTRowFormat('question_d', '选项D'),
                selectFormat('submit_question_answer', '答案', _self.answerArray)
                // common.BTRowFormatEdSelect2(_self, 'submit_question_answer', '答案', 'answerType'),
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_q', row, 'docdetailquestion_id', $documentDetailTable)
                }
            });
            common.changeTableClass($documentDetailTable)
        }

        function initFileTable() {
            let columns;
            columns = [
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
            let params ={
                document_id:common.getUrlParams('document_id'),
                user_id: _self.pagePara.userInfo.user_id
            }

            this.$http.post(apiUrl + 'submit_soq', params).then((response)=>{
                let retData = response.data.info;
                _self.read_state = retData.read_state;
                $('#read_state').val(_self.read_state).trigger('change');
                common.dealSuccessCommon('已提交');
            }, (error)=>{
                common.dealErrorCommon(this, error)
            });

        }
    }
}
</script>
<style scoped>
</style>
