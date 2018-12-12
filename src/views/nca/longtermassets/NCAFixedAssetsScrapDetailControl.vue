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
                        <span style="position: absolute;right:180px;top:12px;" id="FixedInfo"></span>
                    </div>
                    <div class="panel-body">
                        <div class="form-inline" role="form">
                            <div class="form-group" style="margin-bottom: 10px">
                                <div class="form-group">
                                    <input class="form-control"  id="search_textFAS" placeholder="搜索资产编号、固定名称" style="width: 230px;">
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-on:click="addM">-->
                                        <!--<i class="glyphicon glyphicon-plus">新增资产报废单</i>-->
                                    <!--</button>-->
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="saveApplyFixedeScrsp" >提交</button>
                            </div>
                        </div>
                        <table id="FixedAssetsScrspDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <!--<div class="modal fade" id="AddFixedAssetsScrspModal">-->
            <!--<div class="modal-dialog" style="width: 1100px">-->
                <!--<div class="modal-content">-->
                    <!--<div class="modal-header">-->
                        <!--<button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>-->
                        <!--<h4 class="modal-title">新增资产报废单</h4>-->
                    <!--</div>-->
                    <!--<div class="modal-body">-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<input class="form-control"  id="FixedASDC" placeholder="搜索资产编号、名称" v-on:input="getFixedAssetsScrsp">-->
                        <!--</div>-->
                        <!--<div class="form-group col-xs-3">-->
                            <!--<button type="button" class="btn btn-info" v-on:click="addFixedAssetsScrspApplyDetail">确认</button>-->
                        <!--</div>-->
                        <!--<table id="tableFASDC"></table>-->
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
            FixedInfo: ''
        }
    },
    name: 'NCAFixedAssetsScrapDetailControl',
    mounted: function() {
        let _self = this;
        let $FixedAssetsScrspDetailTable = $('#FixedAssetsScrspDetailTable');
        let $tableFASDC = $('#tableFASDC');

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#scrap_state'), _self.pagePara.purchstate);

            _self.longassetsscrap_id = common.getUrlParams('longassetsscrap_id');
            _self.longassetsscrap_no = common.getUrlParams('longassetsscrap_no');

            response = await _self.$http.post(apiUrl + 'search_adt', {longassetsscrap_id:_self.longassetsscrap_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            $('#scrap_state').val(_self.apply.scrap_state).trigger('change');

            if (_self.longassetsscrap_no != null) {
                let FixedInfo = '报废单编号：' + _self.longassetsscrap_no;
                $("#FixedInfo").html(FixedInfo);
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
            params.search_text=$('#search_textFAS').val();
            params.scrap_type=1;
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
                    common.rowDeleteWithApi(_self, '报废申请单详情删除', apiUrl + 'delete_d', $FixedAssetsScrspDetailTable, row, 'longassetsscrapdetail_id', function() {})
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('fixedassets_no', '固定资产编号'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('fixedassets_unit', '计量单位'),
                common.BTRowFormatEditableRight('return_price', '可回收金额'),
                common.BTRowFormatEditableRight('expend_price', '消耗费用'),
                common.actFormatter('act', () => {
                    return '<a class="btn btn-info btn-xs m-r-5 delete_stockApplyDetail">删除</a>';
                }, tableEvents)
            ];
            $FixedAssetsScrspDetailTable.bootstrapTable({
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_d', row, 'longassetsscrapdetail_id', $FixedAssetsScrspDetailTable)
                }
            });
            common.changeTableClass($FixedAssetsScrspDetailTable)
        }
        function queryParamsMateriel(params) {
            params.matNameOrCodeOrFormat = $('#FixedASDC').val();
            params.scrap_type = 1;
            return JSON.stringify(params)
        }
        function initTableMateriel() {
            $tableFASDC.bootstrapTable({
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
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormatAlignLeft('fixedassets_unit', '计量单位'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'fixedassets_category', '固定资产分类', 'fixedassetstype')
                ],
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
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
            common.changeTableClass($tableFASDC)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            $('#FixedAssetsScrspDetailTable').bootstrapTable("refresh")
        },
        // addM: function(event) {
        //     let _self = this;
        //     if(_self.apply.scrap_state==1){
        //         return common.dealPromptCommon('该申请单待审批')
        //     }else if (_self.apply.scrap_state==3){
        //         return common.dealPromptCommon('该申请单已通过')
        //     }
        //     $('#AddFixedAssetsScrspModal').modal('show');
        //     _self.searchedRow = [];
        //     $("#FixedASDC").val('');
        //     $('#tableFASDC').bootstrapTable("refresh");
        //     $("input[type=checkbox]").each(function() {
        //         $(this).prop("checked", false);
        //     })
        // },
        // getFixedAssetsScrsp:function(event){
        //     let _self = this;
        //     $('#tableFASDC').bootstrapTable("refresh")
        // },
        // addFixedAssetsScrspApplyDetail:function(event){
        //     let _self = this;
        //     let params=[];
        //
        //     for(let s of _self.searchedRow){
        //         params.push({
        //             longassetsscrap_id:_self.longassetsscrap_id,
        //             fixedasset_id:s.fixedassetscheckdetail_id
        //         })
        //     }
        //     _self.$http.post(apiUrl + 'add_db', params).then((response) => {
        //         let retData = response.data.info;
        //         $('#FixedAssetsScrspDetailTable').bootstrapTable("refresh");
        //         _self.searchedRow=[];
        //         $('#AddFixedAssetsScrspModal').modal('hide');
        //         common.dealSuccessCommon('保存成功');
        //     }, (response) => {
        //         common.dealErrorCommon(_self, response)
        //     });
        //
        //     $('#AddModal').modal('hide')
        // },
        saveApplyFixedeScrsp:function(event){
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
                $('#tableFASDC').bootstrapTable("refresh");
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