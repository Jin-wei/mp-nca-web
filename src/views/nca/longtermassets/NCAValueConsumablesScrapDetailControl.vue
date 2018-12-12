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
                        <span style="position: absolute;right:180px;top:12px;" id="ValueInfo"></span>
                    </div>
                    <div class="panel-body">
                            <div class="form-inline" role="form">
                                <div class="form-group" style="margin-bottom: 10px">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_VCS" placeholder="搜索易耗品编号、易耗品名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group ">
                                        <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                            <i class="fa fa-search"></i>
                                        </button>
                                        <!--<button class="btn btn-info" v-on:click="addVCS">-->
                                            <!--<i class="glyphicon glyphicon-plus">新增资产报废单</i>-->
                                        <!--</button>-->
                                    </div>
                                </div>
                                <div class="form-group pull-right">
                                    <button class="btn btn-info" v-on:click="saveApplyValueConsumables" >提交</button>
                                </div>
                            </div>
                        <table id="valueConsumablesScrapDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <!--<div class="modal fade" id="AddValueConsumablesModal">-->
            <!--<div class="modal-dialog" style="width: 1100px">-->
                <!--<div class="modal-content">-->
                    <!--<div class="modal-header">-->
                        <!--<button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>-->
                        <!--<h4 class="modal-title">新增资产报废单</h4>-->
                    <!--</div>-->
                    <!--<div class="modal-body">-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<input class="form-control"  id="ValueCS" placeholder="搜索资产编号、名称" v-on:input="getValueConsumables">-->
                        <!--</div>-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<button type="button" class="btn btn-info" v-on:click="addValueConsumablesApplyDetail">确认</button>-->
                        <!--</div>-->
                        <!--<table id="tableValueConsumables"></table>-->
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
            ValueInfo: ''
        }
    },
    name: 'NCAValueConsumablesScrapDetailControl',
    mounted: function() {
        let _self = this;
        let $valueConsumablesScrapDetailTable = $('#valueConsumablesScrapDetailTable');
        let $tableValueConsumables = $('#tableValueConsumables');

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
                let ValueInfo = '报废单编号：' + _self.longassetsscrap_no;
                $("#ValueInfo").html(ValueInfo);
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
            params.search_text=$('#search_text_VCS').val();
            params.scrap_type=3;
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
                    common.rowDeleteWithApi(_self, '报废申请单详情删除', apiUrl + 'delete_d', $valueConsumablesScrapDetailTable, row, 'longassetsscrapdetail_id', function() {})
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                common.BTRowFormat('consumables_specifications', '规格型号'),
                common.BTRowFormat('consumables_unit', '计量单位'),
                common.BTRowFormatEditableRight('return_price', '可回收金额'),
                common.BTRowFormatEditableRight('expend_price', '消耗费用'),
                common.actFormatter('act', () => {
                    return '<a class="btn btn-info btn-xs m-r-5 delete_stockApplyDetail">删除</a>';
                }, tableEvents)
            ];
            $valueConsumablesScrapDetailTable.bootstrapTable({
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_d', row, 'longassetsscrapdetail_id', $valueConsumablesScrapDetailTable)
                }
            });
            common.changeTableClass($valueConsumablesScrapDetailTable)
        }
        function queryParamsMateriel(params) {
            params.matNameOrCodeOrFormat = $('#ValueCS').val();
            params.scrap_type = 3;
            return JSON.stringify(params)
        }
        function initTableMateriel() {
            $tableValueConsumables.bootstrapTable({
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
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormatAlignLeft('consumables_unit', '计量单位')
                ],
                idField: 'consumables_detail_id',
                uniqueId: 'consumables_detail_id',
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
            common.changeTableClass($tableValueConsumables)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            $('#valueConsumablesScrapDetailTable').bootstrapTable("refresh")
        },
        // addVCS: function(event) {
        //     let _self = this;
        //     if(_self.apply.scrap_state==1){
        //         return common.dealPromptCommon('该申请单待审批')
        //     }else if (_self.apply.scrap_state==3){
        //         return common.dealPromptCommon('该申请单已通过')
        //     }
        //     $('#AddValueConsumablesModal').modal('show');
        //     _self.searchedRow = [];
        //     $("#ValueCS").val('');
        //     $('#tableValueConsumables').bootstrapTable("refresh");
        //     $("input[type=checkbox]").each(function() {
        //         $(this).prop("checked", false);
        //     })
        // },
        // getValueConsumables:function(event){
        //     let _self = this;
        //     $('#tableValueConsumables').bootstrapTable("refresh")
        // },
        // addValueConsumablesApplyDetail:function(event){
        //     let _self = this;
        //     let params=[];
        //
        //     for(let s of _self.searchedRow){
        //         params.push({
        //             longassetsscrap_id:_self.longassetsscrap_id,
        //             fixedasset_id:s.consumables_detail_id
        //         })
        //     }
        //     _self.$http.post(apiUrl + 'add_db', params).then((response) => {
        //         let retData = response.data.info;
        //         $('#valueConsumablesScrapDetailTable').bootstrapTable("refresh");
        //         _self.searchedRow=[];
        //         $('#AddValueConsumablesModal').modal('hide');
        //         common.dealSuccessCommon('保存成功');
        //     }, (response) => {
        //         common.dealErrorCommon(_self, response)
        //     });
        //
        //     $('#AddModal').modal('hide')
        // },
        saveApplyValueConsumables:function(event){
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
                $('#tableValueConsumables').bootstrapTable("refresh");
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