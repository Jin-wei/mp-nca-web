<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">资产报废管理</a></li>
            <li class="active">资产报废管理申请单详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                        </div>
                        <h4 class="panel-title">资产报废管理申请单详情</h4>
                        <span style="position: absolute;right:180px;top:12px;" id="DeferredInfo"></span>
                    </div>
                    <div class="panel-body">
                            <div class="form-inline" role="form">
                                <div class="form-group" style="margin-bottom: 10px">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_DASDC" placeholder="搜索项目编号、项目名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                            <i class="fa fa-search"></i>
                                        </button>
                                        <!--<button class="btn btn-info" v-on:click="addD">-->
                                            <!--<i class="glyphicon glyphicon-plus">新增资产报废单</i>-->
                                        <!--</button>-->
                                    </div>
                                </div>
                                <div class="form-group pull-right">
                                    <button class="btn btn-info" v-on:click="saveApplyDeferred">提交</button>
                                </div>
                            </div>
                        <table id="deferredScrapDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <!--<div class="modal fade" id="AdddeferredScrapModal">-->
            <!--<div class="modal-dialog" style="width: 1100px">-->
                <!--<div class="modal-content">-->
                    <!--<div class="modal-header">-->
                        <!--<button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>-->
                        <!--<h4 class="modal-title">新增资产报废单</h4>-->
                    <!--</div>-->
                    <!--<div class="modal-body">-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<input class="form-control"  id="DeferredASC" placeholder="搜索资产编号、名称" v-on:input="getDeferredScrap">-->
                        <!--</div>-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<button type="button" class="btn btn-info" v-on:click="addDeferredScrapApplyDetail">确认</button>-->
                        <!--</div>-->
                        <!--<table id="tableDASDC"></table>-->
                    <!--</div>-->
                <!--</div>-->
            <!--</div>-->
        <!--</div>-->
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAAssetRetirementControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            longassetsscrap_id:'',
            longassetsscrap_no:'',
            apply:{},
            searchedRow:[],
            DeferredInfo: ''
        }
    },
    name: 'NCADeferredAssetsScrapDetailControl',
    mounted: function() {
        let _self = this;
        let $deferredScrapDetailTable = $('#deferredScrapDetailTable');
        let $tableDASDC = $('#tableDASDC');

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#scrap_state'), _self.pagePara.purchaseApplyType);

            _self.longassetsscrap_id = common.getUrlParams('longassetsscrap_id');
            _self.longassetsscrap_no = common.getUrlParams('longassetsscrap_no');

            response = await _self.$http.post(apiUrl + 'search_adt', {longassetsscrap_id:_self.longassetsscrap_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            $('#scrap_state').val(_self.apply.scrap_state).trigger('change');

            if (_self.longassetsscrap_no != null) {
                let DeferredInfo = '报废单编号：' + _self.longassetsscrap_no;
                $("#DeferredInfo").html(DeferredInfo);
            }

            initAllDemandTable();
            initTableMateriel();
            common.reSizeCall();
        }
        function initTableNum(value, row) {
            return 0
        }
        function queryParams(params) {
            params.longassetsscrap_id=_self.longassetsscrap_id;
            params.search_text=$('#search_text_DASDC').val();
            params.scrap_type=2;
            return JSON.stringify(params)
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .delete_stockApplyDetail': function(e, value, row, index) {
                    if(_self.apply.scrap_state==1){
                        return common.dealPromptCommon('该申请单待审批')
                    } else if (_self.apply.scrap_state==3){
                        return common.dealPromptCommon('该申请单已通过')
                    }
                    common.rowDeleteWithApi(_self, '报废申请单详情删除', apiUrl + 'delete_d', $deferredScrapDetailTable, row, 'longassetsscrapdetail_id', function() {})
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('amortize_code', '项目编号'),
                common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                common.BTRowFormatEditableRight('return_price', '可回收金额'),
                common.BTRowFormatEditableRight('expend_price', '消耗费用'),
                common.actFormatter('act', () => {
                    return '<a class="btn btn-info btn-xs m-r-5 delete_stockApplyDetail">删除</a>';
                }, tableEvents)
            ];
            $deferredScrapDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'longassetsscrapdetail_id',
                uniqueId: 'longassetsscrapdetail_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_d', row, 'longassetsscrapdetail_id', $deferredScrapDetailTable)
                }
            });
            common.changeTableClass($deferredScrapDetailTable)
        }
        function queryParamsMateriel(params) {
            params.matNameOrCodeOrFormat = $('#DeferredASC').val();
            params.scrap_type = 2;
            return JSON.stringify(params)
        }
        function initTableMateriel() {
            $tableDASDC.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_a',
                queryParams: queryParamsMateriel,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    {
                        field: 'state',
                        checkbox: true
                    },
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormat('amortize_name', '项目名称')
                ],
                idField: 'amortize_id',
                uniqueId: 'amortize_id',
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10,15,25,50,100],
                locale: 'zh-CN',
                onCheckAll: function(rows) {
                    for (let i = 0; i < rows.length; i++) {
                        _self.searchedRow.push(rows[i])
                    }
                },
                onUncheckAll: function(rows) {
                    _self.searchedRow = []
                },
                onCheck: function(row, $element) {
                    _self.searchedRow.push(row)
                },
                onUncheck: function(row, $element) {
                    _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                }
            });
            common.changeTableClass($tableDASDC)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            $('#deferredScrapDetailTable').bootstrapTable("refresh")
        },
        // addD: function(event) {
        //     let _self = this;
        //     if(_self.apply.scrap_state==1){
        //         return common.dealPromptCommon('该申请单待审批')
        //     }else if (_self.apply.scrap_state==3){
        //         return common.dealPromptCommon('该申请单已通过')
        //     }
        //     $('#AdddeferredScrapModal').modal('show');
        //     _self.searchedRow = [];
        //     $("#DeferredASC").val('');
        //     $('#tableDASDC').bootstrapTable("refresh");
        //     $("input[type=checkbox]").each(function() {
        //         $(this).prop("checked", false);
        //     })
        // },
        // getDeferredScrap:function(event){
        //     let _self = this;
        //     $('#tableDASDC').bootstrapTable("refresh")
        // },
        // addDeferredScrapApplyDetail:function(event){
        //     let _self = this;
        //     let params=[];
        //
        //     for(let s of _self.searchedRow){
        //         params.push({
        //             longassetsscrap_id:_self.longassetsscrap_id,
        //             fixedasset_id:s.amortize_id
        //         })
        //     }
        //     _self.$http.post(apiUrl + 'add_db', params).then((response) => {
        //         let retData = response.data.info;
        //         $('#deferredScrapDetailTable').bootstrapTable("refresh");
        //         _self.searchedRow=[];
        //         $('#AdddeferredScrapModal').modal('hide');
        //         common.dealSuccessCommon('保存成功');
        //     }, (response) => {
        //         common.dealErrorCommon(_self, response)
        //     });
        //
        //     $('#AddModal').modal('hide')
        // },
        saveApplyDeferred:function(event){
            let _self = this;
            if(_self.apply.scrap_state==1){
                return common.dealPromptCommon('该申请单待审批')
            }else if (_self.apply.scrap_state==3){
                return common.dealPromptCommon('该申请单已通过')
            }
            let params={
                longassetsscrap_id:_self.longassetsscrap_id,
                longassetsscrap_no:_self.longassetsscrap_no
            }
            _self.$http.post(apiUrl + 'submit', params).then((response) => {
                let retData = response.data.info;
                $('#tableDASDC').bootstrapTable("refresh");
                _self.searchedRow=[];
                common.dealSuccessCommon('提交成功');
                window.history.back()
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        }
    }
}
</script>
<style scoped>
</style>