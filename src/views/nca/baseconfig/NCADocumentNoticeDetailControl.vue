<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">文件通知管理</a></li>
        <li class="active">内部文件详情</li>
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
                        <button style="position: absolute;right:30px;"  id="saveApply" class="btn btn-info btn-xs" v-show="read_state == 0" v-on:click="saveNoticeDocument">提交</button>
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">文件详情</h4>
                </div>

                <div class="panel-body">
                    <table id="documentNoticeDetailTable"></table>
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
</div>
</template>
<script>
import Vue from 'vue'
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
            docdetail_id: ''
        }
    },
    name: 'NCADocumentNoticeDetailControl',
    mounted: function() {
        let _self = this;
        let $documentNoticeDetailTable = $('#documentNoticeDetailTable');
        let $fileTable = $('#documentFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#read_state'), _self.pagePara.stateType);

            _self.document_id = common.getUrlParams('document_id');
            response = await _self.$http.post(apiUrl + 'search_d', {document_id:_self.document_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.document =_self.apply.document;
            _self.docuserstate =_self.apply.docuserstate;
            _self.read_state = _self.docuserstate.read_state;
            $('#read_state').val(_self.docuserstate.read_state).trigger('change');
            let retData = response.data.info;

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
            params.document_id = _self.$route.query.document_id;
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看试题</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCATestNoticeControl',
                        query: row
                    })
                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFormatterAlignLeft('clause_title', '条款',common.remarkFormatter),
                common.BTRowFormatAlignLeft('user_ids', '对应责任人'),
                common.BTRowFormatEdSelect2Disabled(_self, 'read_state', '状态', 'stateType'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $documentNoticeDetailTable.bootstrapTable({
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
                locale: 'zh-CN'
            });
            common.changeTableClass($documentNoticeDetailTable)
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
                height:200,
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
        saveNoticeDocument:function(event){
            let _self = this;
            let params={
                document_id:_self.document_id
            }
            _self.$http.post(apiUrl + 'submit_q', params).then((response) => {
                let retData = response.data.info;
                _self.read_state = retData.read_state;
                $('#read_state').val(_self.read_state).trigger('change');
                common.dealSuccessCommon('提交成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        }
    }
}
</script>
<style scoped>
</style>
