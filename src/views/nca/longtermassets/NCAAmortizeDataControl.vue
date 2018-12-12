<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">待摊资产数据列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">待摊资产数据列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索待摊资产编号、名称" style="width: 230px;">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-pane fade active in" id="set_table">
                            <table id="setTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="ShowModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <!--<h4 class="modal-title">新增研发项目记录</h4>-->
                        <!--<h4 v-if="saveId !== ''"class="modal-title">修改研发项目记录</h4>-->
                        <h4 class="modal-title">查看研发项目记录</h4>
                    </div>
                    <form @submit.prevent="SaleOrder" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="row" style="margin-top: 10px">
                                    <div class="form-group">
                                        <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门</label>
                                        <div class="col-md-9">
                                            <input class="form-control" id="department_id" data-parsley-required="true" disabled>
                                        </div>
                                    </div>
                                </div>
                                <div class="row" style="margin-top: 10px">
                                    <div class="form-group">
                                        <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门名称</label>
                                        <div class="col-md-9">
                                            <input class="form-control" id="department_name" data-parsley-required="true" disabled>
                                        </div>
                                    </div>
                                </div>
                                <div class="row" style="margin-top: 10px">
                                    <div class="form-group">
                                        <label class="col-md-2 control-label"><span class="table-required">*</span>管理责任人</label>
                                        <div class="col-md-9" >
                                            <input class="form-control" id="amortize_manager" data-parsley-required="true" disabled>
                                        </div>
                                    </div>
                                </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="Act">确认</button>
                        </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeDataControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                oldRow:{}
            }
        },
        name: 'NCAAmortizeDataControl',
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');

            window.designEvents = {
                'change .fileupload': function(e, value, row, index) {
                    common.fileFileUploadRefresh(this, _self, row, apiUrl, 'amortize_update', $setTable, 'amortize_id')
                },
                'click .delete-button': function(e, value, row, index) {
                    common.deleteFilesRRefresh(this, _self, row, apiUrl, 'delete_file', $setTable, 'amortize_id')
                }
            }


            function operateFormatter(value, row, index) {
                return [
                    '<a class="table_edit btn btn-info btn-xs m-r-5" style="cursor: pointer;">查看</a>',
                ].join('')
            }
            window.tableEvents = {
                'click .table_edit': function(e, value, row, index) {

                    $('#ShowModal').modal('show');
                    // console.log(6666)
                    // if(row.develop_project_state === 3){
                    //     console.log(3333)
                    //     console.log(row.develop_id,'row')
                    _self.saveId = row.amortize_id;
                    _self.workRow = $.extend(true, {}, row)
                    $('#department_id').val(_self.workRow.department_id).trigger('change');
                    $('#department_name').val(_self.workRow.department_name).trigger('change');
                    $('#amortize_manager').val(_self.workRow.amortize_manager).trigger('change');

                    // }else{
                    //     return common.dealPromptCommon('该项目不是拒绝状态，不能编辑')
                    // }
                },
            }
            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '待摊资产编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '待摊资产名称'),
                    common.BTRowFormat('amortize_agelimit', '预计使用年限'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'amortize_way', '摊销方法', 'amortizedInfo'),
                    common.BTRowFormatEditableRight('amortize_already_mos', '已摊销月数'),
                    common.BTRowFormatEditableRight('amortize_already_money', '已累计摊销金额'),
                    common.BTRowFormatEditableRight('amortize_surplus_mos', '剩余摊销月数'),
                    // common.BTRowFormat('department_id', '归属部门编号'),
                    // common.BTRowFormatAlignLeft('department_name', '归属部门名称'),
                    common.BTRowFormat('amortize_acceptor_time', '验收移交时间'),
                    common.BTRowFormatWithFE('files', '资产照片', common.filesFormatterWithUpload, designEvents),
                    // common.BTRowFormatAlignLeft('amortize_manager', '管理责任人'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                    common.BTRowFormatWithFE('', '操作',operateFormatter,tableEvents)
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortize_id',
                    uniqueId: 'amortize_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'amortized_id', $setTable)
                    }

                });
                common.changeTableClass($setTable)
                common.initImageViewer()
            }

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                initsetTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            },
            SaleOrder: function (){
                $('#ShowModal').modal('hide')
            }
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
