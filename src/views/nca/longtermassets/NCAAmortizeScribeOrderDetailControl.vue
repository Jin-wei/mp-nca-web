<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">长期资产管理</a></li>
            <li class="active">材料申购单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料申购单</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索物料名称">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <div class="form-group">
                                    <button id="submitSubscribe" class="btn btn-info" v-on:click="submitSubscribe">提交</button>
                                </div>
                                <div class="form-group">
                                    <button id="addSubscribe" class="btn btn-info" v-on:click="addSubscribe">增加</button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="tab-content" style="padding: 15px">
                        <table id="subscribeAmortizeTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <!--申购addmodal-->
        <div class="modal fade" id="AddModalSubcribe" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增申购项记录</h4>
                    </div>
                    <form @submit.prevent="saveSubscribe" id="formB">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>物料名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="subscribe_name" v-model="workRow.subscribeorderdetail_name" data-parsley-group="block-1" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.subscribeorderdetail_format" id="subscribe_format" data-parsley-group="block-1" >
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control select2" v-model="workRow.subscribeorderdetail_unit" multiple style="width:100%" id="subscribe_unit"  data-parsley-group="block-1" >
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px">数量</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="subscribe_number" v-model="workRow.subscribeorderdetail_number" data-parsley-group="block-1" data-parsley-type="number" data-parsley-required="true">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10" style=" padding-left: 11px;">
                                        <input class="form-control select2" id="subscribe_remark" v-model="workRow.subscribeorderdetail_remark" >
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveAction">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <!--<div class="tab-content" v-show="_self.state === 2">-->
            <!--<table id="subscribeDevelopTable"></table>-->
        <!--</div>-->
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeScribeOrderControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                state: '',
                workRow:'',
                developInfo:[],
                amortizeInfo:[],
            }
        },
        name: 'NCAAmortizeScribeOrderDetailControl',
        mounted: function() {
            let _self = this;
            _self.amortizesubscribeorder_id = _self.$route.query.amortizesubscribeorder_id;
            // _self.developsubscribeorder_id = _self.$route.query.developsubscribeorder_id;
            // if(_self.amortizesubscribeorder_id === undefined){
            //     _self.state = 2
            // }
            // if(_self.developsubscribeorder_id === undefined){
            //     _self.state = 1
            // }

            let $subscribeAmortizeTable = $('#subscribeAmortizeTable');
            // let $subscribeDevelopTable = $('#subscribeDevelopTable');

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>'
                ].join('')
            }

            function queryAmortizeParams(params) {
                params.search_text=$('#search_text').val();
                params.amortizesubscribeorder_id = _self.amortizesubscribeorder_id;
                return JSON.stringify(params)
            }
            function initAmortizeTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                        console.log(_self.subscribeorder_state,'_self.subscribeorder_state')
                        if (_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1) {
                            if (_self.subscribeorder_state === 2) {
                                common.dealPromptCommon('申购单已通过，不能提交审核')
                                return
                            }
                            common.dealPromptCommon('申购单已提交，不能再次提交审核')
                            return
                        } else {
                            common.dealConfrimCommon('您确定删除吗？', function () {
                                _self.$http.post(apiUrl + 'deleteAmortizeSubscribeOrderDetail', {amortizesubscribeorderdetail_id: row.amortizesubscribeorderdetail_id}).then((response) => {
                                    $('#subscribeAmortizeTable').bootstrapTable("refresh");
                                }, (response) => {
                                    return common.dealPromptCommon('删除失败');
                                });
                            });
                        }
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEditableLeft('subscribeorderdetail_name', '物料名称'),
                    common.BTRowFormatEditable('subscribeorderdetail_format', '规格型号'),
                    common.BTRowFormatEditable('subscribeorderdetail_unit', '计量单位'),
                    common.BTRowFormatEditableRight('subscribeorderdetail_number', '数量'),
                    // common.BTRowFormatEditable('subscribeorderdetail_remark', '备注'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('subscribeorderdetail_remark', '备注',120,7),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents),
                ];
                $subscribeAmortizeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getAmortizeSubscribeOrderDetail',
                    queryParams: queryAmortizeParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribeorderdetail_id',
                    uniqueId: 'amortizesubscribeorderdetail_id',
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
                    onEditableSave: async (field, row, oldValue, $el) => {
                        if (_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1) {
                            if (_self.subscribeorder_state === 2) {
                                common.dealPromptCommon('申购单已通过，不能对其进行修改')
                                $('#subscribeAmortizeTable').bootstrapTable("refresh")
                                return
                            }
                            common.dealPromptCommon('申购单已提交，不能对其进行修改')
                            $('#subscribeAmortizeTable').bootstrapTable("refresh")
                            return
                        } else {
                            let response = await _self.$http.post(apiUrl + 'modifyAmortizeSubscribeOrderDetail', {
                                'old': _self.oldRow,
                                'new': row
                            });
                            $subscribeAmortizeTable.bootstrapTable('updateByUniqueId', {
                                id: row.amortizesubscribeorderdetail_id,
                                row: response.data.info
                            });
                        }
                    }

                });
                common.changeTableClass($subscribeAmortizeTable)
            }

            // function actFormatter(value, row, index) {
            //     return [
            //         '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
            //     ].join('')
            // }
            // function queryDevelopParams(params) {
            //     params.search_text=$('#search_text').val();
            //     params.developsubscribeorder_id = _self.developsubscribeorder_id;
            //     return JSON.stringify(params)
            // }
            // function initDevelopTable() {
            //     window.tableEvents = {
            //         'click .tableDelete': function(e, value, row, index) {
            //             console.log(_self.developInfo,'_self.subscribeorder_state3333')
            //             if (_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1) {
            //                 if (_self.subscribeorder_state === 2) {
            //                     common.dealPromptCommon('申购单已通过，不能提交审核')
            //                     return
            //                 }
            //                 common.dealPromptCommon('申购单已提交，不能再次提交审核')
            //                 return
            //             } else {
            //                 common.dealConfrimCommon('您确定删除么', function () {
            //                     _self.$http.post(apiUrl + 'deleteDevelopSubscribeOrderDetail', {developsubscribeorderdetail_id: row.developsubscribeorderdetail_id}).then((response) => {
            //                         $('#subscribeDevelopTable').bootstrapTable("refresh");
            //                     }, (response) => {
            //                         return common.dealPromptCommon('删除失败');
            //                     });
            //                 });
            //             }
            //         }
            //     };
            //     let columsArr = [
            //         common.BTRowFormatWithIndex('NO.'),
            //         common.BTRowFormatEditable('subscribeorderdetail_name', '物料名称'),
            //         common.BTRowFormatEditable('subscribeorderdetail_format', '规格型号'),
            //         common.BTRowFormatEditable('subscribeorderdetail_unit', '计量单位'),
            //         common.BTRowFormatEditable('subscribeorderdetail_number', '数量'),
            //         common.BTRowFormatEditable('subscribeorderdetail_remark', '备注'),
            //         common.BTRowFormatWithFE('', '操作', actFormatter, tableEvents),
            //     ];
            //     $subscribeDevelopTable.bootstrapTable({
            //         method: 'POST',
            //         url: apiUrl + 'getDevelopSubscribeOrderDetail',
            //         queryParams: queryDevelopParams,
            //         sidePagination: 'server',
            //         ajaxOptions: common.bootstrapTableAjaxOtions,
            //         responseHandler: function(res) {
            //             return res.info;
            //         },
            //         height: common.getTableHeight(),
            //         columns: columsArr,
            //         idField: 'developsubscribeorderdetail_id',
            //         uniqueId: 'developsubscribeorderdetail_id',
            //         striped: true,
            //         clickToSelect: true,
            //         showRefresh: false,
            //         pagination: true,
            //         pageSize: common.pageSize(),
            //         pageList: [10, 15, 25, 50, 100],
            //         locale: 'zh-CN',
            //         onEditableShown: (field, row, $el, editable) => {
            //             _self.oldRow = $.extend(true, {}, row)
            //         },
            //         onEditableSave: async (field, row, oldValue, $el) => {
            //             if (_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1) {
            //                 if (_self.subscribeorder_state === 2) {
            //                     common.dealPromptCommon('申购单已通过，不能对其进行修改')
            //                     $('#subscribeDevelopTable').bootstrapTable("refresh")
            //                     return
            //                 }
            //                 common.dealPromptCommon('申购单已提交，不能对其进行修改')
            //                 $('#subscribeDevelopTable').bootstrapTable("refresh")
            //                 return
            //             } else {
            //                 let response = await _self.$http.post(apiUrl + 'modifyDevelopSubscribeOrderDetail', {
            //                     'old': _self.oldRow,
            //                     'new': row
            //                 });
            //                 $subscribeDevelopTable.bootstrapTable('updateByUniqueId', {
            //                     id: row.developsubscribeorderdetail_id,
            //                     row: response.data.info
            //                 });
            //             }
            //         }
            //
            //     });
            //     common.changeTableClass($subscribeDevelopTable)
            // }
            async function initData() {
                initAmortizeTable();
                // initDevelopTable();
                // if(_self.developsubscribeorder_id === undefined){
                    let response = await _self.$http.post(apiUrl + 'getAmortizeSubscribeOrder', {amortizesubscribeorder_id:_self.amortizesubscribeorder_id});
                    for(let amortizeInfo of response.data.info.rows){
                        _self.amortizesubscribeorder_id = amortizeInfo.amortizesubscribeorder_id
                        if(_self.amortizesubscribeorder_id == _self.$route.query.amortizesubscribeorder_id){
                            _self.subscribeorder_state = amortizeInfo.subscribeorder_state
                            console.log(_self.subscribeorder_state,'subscribeorder_state')
                            return
                        }
                    }
                // }
                // if(_self.amortizesubscribeorder_id === undefined){
                //     let response = await _self.$http.post(apiUrl + 'getDevelopSubscribeOrder', {developsubscribeorder_id:_self.developsubscribeorder_id});
                //     for(let developInfo of response.data.info.rows){
                //         _self.developsubscribeorder_id = developInfo.developsubscribeorder_id
                //         if(_self.developsubscribeorder_id == _self.$route.query.developsubscribeorder_id){
                //             _self.subscribeorder_state = developInfo.subscribeorder_state
                //             console.log(_self.subscribeorder_state,'subscribeorder_state')
                //             return
                //         }
                //     }
                // }
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                // $('#subscribeDevelopTable').bootstrapTable("refresh");
                $('#subscribeAmortizeTable').bootstrapTable("refresh");
            },
            addSubscribe:function (event) {
                let _self = this;
                _self.workRow = {};
                let formB = $('#formB');
                if(_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1){
                    if(_self.subscribeorder_state === 2){
                        common.dealPromptCommon('申购单已通过，不能提交审核')
                        return
                    }else{
                        common.dealPromptCommon('申购单已提交，不能再次提交审核')
                        return
                    }
                }else{}
                $('#AddModalSubcribe').modal('show');
            },
            saveSubscribe: async function(event) {
                let _self = this;
                let formB = $('#formB');
                if (formB.parsley().isValid()) {
                    try {
                        let params = {}
                        params.subscribeorderdetail_name = _self.workRow.subscribeorderdetail_name;
                        params.subscribeorderdetail_format =  _self.workRow.subscribeorderdetail_format;
                        params.subscribeorderdetail_unit =  _self.workRow.subscribeorderdetail_unit;
                        params.subscribeorderdetail_number =  _self.workRow.subscribeorderdetail_number;
                        params.subscribeorderdetail_remark =  _self.workRow.subscribeorderdetail_remark;
                        params.amortizesubscribeorder_id = _self.$route.query.amortizesubscribeorder_id;
                        // params.developsubscribeorder_id = _self.$route.query.developsubscribeorder_id;
                        // if(params.developsubscribeorder_id === undefined){
                            let response = await _self.$http.post(apiUrl + 'addAmortizeSubscribeOrderDetail', params);
                            $('#subscribeAmortizeTable').bootstrapTable("refresh");
                        // }
                        // if(params.amortizesubscribeorder_id === undefined){
                        //     let response = await _self.$http.post(apiUrl + 'addDevelopSubscribeOrderDetail', params);
                        //     $('#subscribeDevelopTable').bootstrapTable("refresh");
                        // }

                        console.log(params,'params')
                        _self.workRow = {};
                        formB.parsley().reset();
                        $('#AddModalSubcribe').modal('hide');
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            submitSubscribe: function (event) {
                let _self = this;
                if(_self.subscribeorder_state === 2 || _self.subscribeorder_state === 1){
                    if(_self.subscribeorder_state === 2){
                        common.dealPromptCommon('申购单已通过，不能提交审核')
                        return
                    }
                    common.dealPromptCommon('申购单已提交，不能再次提交审核')
                    return
                }else{
                    common.dealConfrimCommon('确定提交审核？', async function () {
                        try {
                            let params = {};
                            params.amortize_id = _self.amortize_id;
                            params.amortizebudget_id  = _self.amortizebudget_id;
                            params.subscribeorderdetail_name = _self.workRow.subscribeorderdetail_name;
                            params.subscribeorderdetail_format =  _self.workRow.subscribeorderdetail_format;
                            params.subscribeorderdetail_unit =  _self.workRow.subscribeorderdetail_unit;
                            params.subscribeorderdetail_number =  _self.workRow.subscribeorderdetail_number;
                            params.subscribeorderdetail_remark =  _self.workRow.subscribeorderdetail_remark;
                            params.amortizesubscribeorder_id = _self.$route.query.amortizesubscribeorder_id;
                            // params.developsubscribeorder_id = _self.$route.query.developsubscribeorder_id;
                            // if(params.developsubscribeorder_id === undefined){
                                let response = await _self.$http.post(apiUrl + 'sendAmortizeSubscribeOrder', params);
                                await $('#subscribeAmortizeTable').bootstrapTable("refresh");
                                console.log(params,'response')
                                _self.subscribeorder_state = 1;
                            // }
                            // if(params.amortizesubscribeorder_id === undefined){
                            //     let response = await _self.$http.post(apiUrl + 'sendDevelopSubscribeOrder', params);
                            //     await $('#subscribeDevelopTable').bootstrapTable("refresh");
                            //     _self.subscribeorder_state = 1;
                            // }
                        } catch (error) {
                            common.dealErrorCommon(_self,error);
                        }
                    });
                    _self.subscribeorder_state = 1;
                }
            },
        }
    }
</script>
<style scoped>
</style>
