<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">固定资产申购管理</a></li>
        <li class="active">固定资产申购单</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">申购单号</span>
                                <div>
                                    <input class="form-control" v-model="apply.fixedassetspurch_no" readonly>
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
                    <h4 class="panel-title">固定资产申购单</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_APDC" placeholder="搜索固定资产名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right">
                            <button class="btn btn-info" v-show="purch_state ==0 || purch_state ==2" v-on:click="addAPDC">增加</button>
                            <button class="btn btn-info" v-show="purch_state ==0 || purch_state ==2" v-on:click="saveAddAssetPurchaseOrder">提交</button>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <table id="assetPurchaseOrderDetailTable"></table>
                </div>

                <div class="modal fade" id="AddAssetPurchaseOrderModal">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">新增固定资产记录</h4>
                            </div>
                            <form @submit.prevent="submitForm" id="formAPD">
                                <div class="modal-body">
                                    <div class="form-horizontal">
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>固定资产名称</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.fixedassets_name" data-parsley-required="true" maxlength="40" data-parsley-maxlength="40">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>规格型号</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.fixedassets_model" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>计量单位</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.fixedassets_unit" data-parsley-required="true" maxlength="7" data-parsley-maxlength="7">
                                            </div>
                                        </div>
                                        <div class="form-group">
                                            <label class="col-md-2 control-label"><span class="table-required">*</span>数量</label>
                                            <div class="col-md-9">
                                                <input class="form-control" v-model="workRow.fixedassets_num" data-parsley-required="true"  data-parsley-type="number">
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
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            fixedassetspurch_id:'',
            apply:{},
            purch_state: ''
        }
    },
    name: 'NCADocumentManagementDetailControl',
    mounted: function() {
        let _self = this;
        let $assetPurchaseOrderDetailTable = $('#assetPurchaseOrderDetailTable');
        let $fileTable = $('#documentFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#purch_state'), _self.pagePara.purchstate);

            _self.fixedassetspurch_id = common.getUrlParams('fixedassetspurch_id');
            response = await _self.$http.post(apiUrl + 'search_pdt', {fixedassetspurch_id:_self.fixedassetspurch_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.purch_state = _self.apply.purch_state;
            $('#purch_state').val(_self.apply.purch_state).trigger('change');

            initAllDemandTable();
            common.reSizeCall();
            $('#formAPD').parsley()
        }

        function queryParams(params) {
            params.search_text=$('#search_text_APDC').val();
            params.fixedassetspurch_id=common.getUrlParams('fixedassetspurch_id');
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 meeting_delete">删除</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .meeting_delete': function (e, value, row, index) {
                    if(_self.purch_state==1){
                        return common.dealPromptCommon('该申请单已提交')
                    }else if (_self.purch_state==3){
                        return common.dealPromptCommon('该申请单已通过')
                    }
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_pd', $assetPurchaseOrderDetailTable, row, 'fixedassetspurchdetail_id', function() {})
                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEditableLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormatEditable('fixedassets_model', '规格型号'),
                common.BTRowFormatEditable('fixedassets_unit', '计量单位'),
                common.BTRowFormatEditableRight('fixedassets_num', '数量'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $assetPurchaseOrderDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_pd',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetspurchdetail_id',
                uniqueId: 'fixedassetspurchdetail_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_pd', row, 'fixedassetspurchdetail_id', $assetPurchaseOrderDetailTable)
                }
            });
            common.changeTableClass($assetPurchaseOrderDetailTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetPurchaseOrderDetailTable').bootstrapTable("refresh")
        },
        addAPDC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#addAPD').show();
            $('#AddAssetPurchaseOrderModal').modal('show');
        },
        saveAddAssetPurchaseOrder:function(event){
            let _self = this;
            let params={
                fixedassetspurch_id: _self.fixedassetspurch_id,
                fixedassetspurch_no: _self.apply.fixedassetspurch_no,
            }
            _self.$http.post(apiUrl + 'submit_p', params).then((response) => {
                let retData = response.data.info;
                _self.purch_state = retData.purch_state;

                $('#purch_state').val(_self.purch_state).trigger('change');
                common.dealSuccessCommon('提交成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },
        submitForm: async function(event) {
            let _self = this;
            if ($('#formAPD').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.fixedassetspurch_id = _self.fixedassetspurch_id

                    let response = await _self.$http.post(apiUrl + 'add_pd', _self.workRow)
                    let retData = response.data.info;
                    $('#assetPurchaseOrderDetailTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    });
                    console.log('success');
                    $('#assetPurchaseOrderDetailTable').bootstrapTable("refresh");
                    $('#formAPD').parsley().reset();
                    $('#AddAssetPurchaseOrderModal').modal('hide')
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
