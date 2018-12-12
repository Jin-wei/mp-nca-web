<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">系统管理</a></li>
            <li class="active">人工价格标准列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">人工价格标准列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索工种名称" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="workerPriceTable"></table>
                    </div>
                </div>

                <div class="modal fade" id="AddWorkerModal" data-backdrop="static">
                    <div class="modal-dialog">
                        <div class="modal-content">
                            <div class="modal-header">
                                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                <h4 class="modal-title">新增人工价格标准</h4>
                            </div>
                            <form @submit.prevent="saveAct" id="formA">
                                <div class="modal-body">
                                    <div class="row">
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>工种名称</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" v-model="workRow.worker_name"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" data-parsley-group="block-1">
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>计量单位</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" v-model="workRow.worker_unit"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" data-parsley-group="block-1">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="row">
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label"><span class="table-required">*</span>人工价格</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                                       oninput="if(value.length>10)value=value.slice(0,10)" v-model="workRow.worker_cost" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" data-parsley-group="block-1">
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="modal-footer">
                                    <button type="submit" class="btn btn-info" id="saveAct">保存</button>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCAWorkerPriceControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{},
                workRow: {},
                oldRow: ''
            }
        },
        name: 'NCAWorkerPriceControl',
        mounted: function() {
            let _self = this;
            let $workerPriceTable = $('#workerPriceTable');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                _self.domain_id = retData.userInfo.domain_id;
                initAllDemandTable();
            }

            function queryParams(params) {
                if (_self.domain_id) {
                    params.domain_id = _self.domain_id
                }
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initAllDemandTable() {
                window.tableEvents = {
                    'click .delete': function(e, value, row, index) {
                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $workerPriceTable, row, 'worker_id', function() {})
                    }
                };
                $workerPriceTable.bootstrapTable({
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
                        common.BTRowFormat('worker_id', '工种ID'),
                        common.BTRowFormatAlignLeft('worker_name', '工种名称'),
                        common.BTRowFormatEditable('worker_unit', '计量单位'),
                        /*common.BTRowFormatEditable('worker_cost', '人工价格'),*/
                        common.BTRowFormatWithFormatterAlignRight('worker_cost', '人工价格',common.priceFormat),
                        common.actFormatter('act', () => {
                            return '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>';
                        }, tableEvents)
                    ],
                    idField: 'worker_id',
                    uniqueId: 'worker_id',
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
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'worker_id', $workerPriceTable)
                    }
                });
                common.changeTableClass($workerPriceTable)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            search: function(event) {
                $('#workerPriceTable').bootstrapTable("refresh")
            },
            addM:function(event){
                let _self = this;
                _self.workRow = {};
                $('#AddWorkerModal').modal('show');
            },
            saveAct: async function(event) {
                let _self = this;
                try {
                    if ($('#formA').parsley().isValid()) {
                        console.log(_self.workRow)
                        let response = await _self.$http.post(apiUrl + 'add', _self.workRow)
                        common.dealSuccessCommon('增加成功');
                        $('#AddWorkerModal').modal('hide')
                        $('#workerPriceTable').bootstrapTable("refresh")
                    }
                    console.log(_self.workRow)
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
</script>
<style scoped>
</style>