<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">行政办公管理</a></li>
        <li class="active">文件发布列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">文件发布列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline">
                        <div class="form-group" v-show="tableType==1">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_all" placeholder="搜索申请单编号" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" id="createTime" placeholder="提交时间"/>
                            </div>
                            <div class="form-group" >
                                <button id="searchAll" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                                <!--<button id="addDMC" class="btn btn-info" v-on:click="addDMC" >增加-->
                                <!--</button>-->
                            </div>
                        </div>
                        <div class="form-group" v-show="tableType==2">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_net" placeholder="搜索申请单编号" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" id="createTimeNet" placeholder="提交时间" />
                            </div>
                            <div class="form-group ">
                                <button id="searchNet" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                                <!--<button id="addDMCO" class="btn btn-info" v-on:click="addDMCO" >增加-->
                                <!--</button>-->
                            </div>
                        </div>
                        <div class="form-group pull-right" v-show="tableType==1" >
                            <button id="addDMC" class="btn btn-info" v-on:click="addDMC" >增加
                            </button>
                        </div>
                        <div class="form-group pull-right" v-show="tableType==2" >
                            <button id="addDMCO" class="btn btn-info" v-on:click="addDMCO" >增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body ">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="showTable(1)">
                            <a href="#inDocument" data-toggle="tab">
                                <span>内部文件</span>
                            </a>
                        </li>
                        <li class="" @click="showTable(2)">
                            <a href="#outDocument" data-toggle="tab">
                                <span>外部文件</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="inDocument">
                            <table id="inDocumentTable"></table>
                        </div>
                        <div class="tab-pane fade" id="outDocument" >
                            <table id="outDocumentTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddDocumentInModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增文件列表</h4>
                </div>
                <form @submit.prevent="submitA" id="formDMCI">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>文件题目</label>
                                <div class="col-md-9">
                                    <input class="form-control"  v-model="workRow.document_title"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>生效日期</label>
                                <div class="col-md-9">
                                    <input type="text" class="form-control" id="document_date" data-parsley-required="true" >
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

    <div class="modal fade" id="AddDocumentOutModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增文件列表</h4>
                </div>
                <form @submit.prevent="submitAO" id="formDMCO">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>文件题目</label>
                                <div class="col-md-9">
                                    <input class="form-control"  v-model="workRow.document_title"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>发文单位</label>
                                <div class="col-md-9">
                                    <input class="form-control"  v-model="workRow.document_unit"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
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
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentManagementControl?method=';
export default {
    data: function() {
        return {
            pagePara:{},
            tableType:1,
            workRow: {},
            oldRow: ''
        }
    },
    name: 'NCADocumentManagementControl',
    mounted: function() {
        let _self = this;
        let $inDocumentTable = $('#inDocumentTable');
        let $outDocumentTable = $('#outDocumentTable');

        async function initData() {
            // common.initDatepicker($('#document_date'));
            common.initDatepicker($('#createTime'));
            common.initDatepicker($('#createTimeNet'));
            console.log('new Date()',new Date())
            $('#document_date').datepicker({
                language: 'zh-CN',
                todayBtn: "linked",
                autoclose: true,
                todayHighlight: true,
                format: 'yyyy-mm-dd',
                startDate:new Date()
            });

            let document_date = $('#document_date').val();

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            common.initSelect2Placeholder($('#document_state'), _self.pagePara.stateType, '状态');

            initAllDemandTable();
            initNetDemandTable();
            common.reSizeCall();
        }
        //内部文件
        function nameFormatter(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer;">',
                value,
                '</a>'
            ].join('')
        }

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 meeting_delete">删除</a>'
            ].join('')
        }

        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                if(_self.tableType==1){
                    _self.$router.push({
                        path: '/nca/baseconfig/NCADocumentManagementDetailControl',
                        query: row
                    })
                }else if (_self.tableType==2){
                    _self.$router.push({
                        path: '/nca/baseconfig/NCATestOutDetailControl',
                        query: {
                            document_id: row.document_id
                        }
                    })
                }
            },
            'click .meeting_delete': function (e, value, row, index) {
                if(_self.tableType==1){
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_doc', $inDocumentTable, row, 'document_id', function() {})
                }else if (_self.tableType==2){
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_doc', $outDocumentTable, row, 'document_id', function() {})
                }
            }
        };

        function queryParamsAll(params) {
            params.search_text=$('#search_text_all').val();
            if($("#createTime").val()) {
                params.created_at = $("#createTime").val();
            }
            params.document_type=1;

            return JSON.stringify(params)
        }
        function initAllDemandTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('document_id', '申请单编号', nameFormatter,tableEvents),
                common.BTRowFormatEditableLeft('document_title', '文件题目'),
                common.BTRowFormat('created_at', '创建时间'),
                common.BTRowFormat('document_check_date', '审批时间'),
                common.BTRowFormatAlignLeft('document_checker_name', '审批人'),
                common.BTRowFormatEdSelect2Disabled(_self, 'document_state', '申请单状态', 'stateType'),
                common.BTRowFormat('document_date', '文件生效日期'),
                common.BTRowFormatWithFormatterAlignLeft('document_refuse_remark', '驳回说明',common.remarkFormatter),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $inDocumentTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_doc',
                queryParams: queryParamsAll,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                sortOrder:'asc',
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'document_id',
                uniqueId: 'document_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_doc', row, 'document_id', $inDocumentTable)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($inDocumentTable)
        }
        //外部文件
        function queryParamsNet(params) {
            params.search_text=$('#search_text_net').val();
            if($("#createTimeNet").val()){
                params.created_at=$("#createTimeNet").val();
            }
            params.document_type=2;

            return JSON.stringify(params)
        }
        function initNetDemandTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('document_id', '申请单编号', nameFormatter,tableEvents),
                common.BTRowFormatAlignLeft('document_title', '文件题目'),
                common.BTRowFormatAlignLeft('document_unit', '发文单位'),
                common.BTRowFormat('created_at', '创建时间'),
                common.BTRowFormat('document_check_date', '审批时间'),
                common.BTRowFormatAlignLeft('document_checker_name', '审批人'),
                common.BTRowFormatEdSelect2Disabled(_self, 'document_state', '申请单状态', 'stateType'),
                common.BTRowFormatWithFormatterAlignLeft('document_refuse_remark', '驳回说明',common.remarkFormatter),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $outDocumentTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_doc',
                queryParams: queryParamsNet,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'document_id',
                uniqueId: 'document_id',
                striped: true,
                clickToSelect: true,
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
            common.changeTableClass($outDocumentTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        showTable:function(index){
            let _self = this;
            _self.tableType=index;
            if(index === 1 ){
                $('#inDocumentTable').bootstrapTable("refresh")
            } else if (index === 2 ){
                $('#outDocumentTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#inDocumentTable').bootstrapTable("refresh")
            } else if (_self.tableType === 2 ){
                $('#outDocumentTable').bootstrapTable("refresh")
            }
        },
        addDMC:async function () {
            let _self = this;
            _self.workRow = {}
            $('#document_date').val(null).trigger('change');
            $('#formDMCI').parsley().reset()
            $('#AddDocumentInModal').modal('show')
        },
        addDMCO:async function () {
            let _self = this;
            _self.workRow = {}
            $('#formDMCO').parsley().reset()
            $('#AddDocumentOutModal').modal('show')
        },
        submitA: async function(event) {
            let _self = this
            if ($('#formDMCI').parsley().isValid()) {
                try {
                    _self.workRow.document_date = $('#document_date').val();
                    _self.workRow.document_type = 1;
                    this.$http.post(apiUrl + 'add_doc', _self.workRow).then((response) => {
                        let retData = response.data.info;
                        $('#inDocumentTable').bootstrapTable("refresh");
                        common.dealSuccessCommon('保存成功');
                    }, (response) => {
                        common.dealErrorCommon(this, response)
                    });
                    console.log('success')
                    $('#AddDocumentInModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        submitAO: async function(event) {
            let _self = this
            if ($('#formDMCO').parsley().isValid()) {
                try {
                    _self.workRow.document_type = 2;
                    this.$http.post(apiUrl + 'add_doc', _self.workRow).then((response) => {
                        let retData = response.data.info;
                        $('#outDocumentTable').bootstrapTable("refresh");
                        common.dealSuccessCommon('保存成功');
                    }, (response) => {
                        common.dealErrorCommon(this, response)
                    });
                    console.log('success')
                    $('#AddDocumentOutModal').modal('hide')
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
