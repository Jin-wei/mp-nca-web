<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">资产盘点单详情</a></li>
            <li class="active">盘点对象详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">盘点单详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_AIUDO" placeholder="搜索固定资产编号、固定资产名称" style="width: 230px;">
                                </div>
                                <div class="form-group" >
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-show="take_stock_detail_status ==0 || take_stock_detail_status ==1" v-on:click="addFixedAssets" >-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 提交-->
                                    <!--</button>-->
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_AIUDT" placeholder="搜索待摊资产编号、待摊资产名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-show="take_stock_detail_status ==0 || take_stock_detail_status ==1" v-on:click="addFixedAssets" >-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 提交-->
                                    <!--</button>-->
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==3">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_AIDOThree" placeholder="搜索低值易耗品资产编号、低值易耗品资产名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-show="take_stock_detail_status ==0 || take_stock_detail_status ==1" v-on:click="addFixedAssets" >-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 提交-->
                                    <!--</button>-->
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#fixedAssetsInventoryData" data-toggle="tab">
                                    <span>固定资产维护</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#DeferredAssetsInventoryData" data-toggle="tab">
                                    <span>待摊资产维护</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#valueConsumablesAssetsInventoryData" data-toggle="tab">
                                    <span>低值易耗品资产维护</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fixedAssetsInventoryData">
                                <table id="fixedAssetsInventoryDataTable"></table>
                            </div>
                            <div class="tab-pane fade" id="DeferredAssetsInventoryData">
                                <table id="deferredAssetsInventoryDataTable"></table>
                            </div>
                            <div class="tab-pane fade" id="valueConsumablesAssetsInventoryData">
                                <table id="valueConsumablesAssetsInventoryDataTable"></table>
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
const apiUrl = '/api/nca/longtermassets/NCATakeStockSRV?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            workRow:{},
            tableType:1,
            index: '',
            take_stock_detail_id: '',
            take_stock_parent_no: '',
            apply:{},
            take_stock_detail_status: ''
        }
    },
    name: 'NCAAssetInventoryUsersTaskDetailControl',
    mounted: function() {
        let _self = this;
        let $fixedAssetsInventoryDataTable = $('#fixedAssetsInventoryDataTable');
        let $deferredAssetsInventoryDataTable = $('#deferredAssetsInventoryDataTable');
        let $valueConsumablesAssetsInventoryDataTable = $('#valueConsumablesAssetsInventoryDataTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            _self.take_stock_detail_id = common.getUrlParams('take_stock_detail_id');
            _self.take_stock_parent_no = common.getUrlParams('take_stock_parent_no');
            response = await _self.$http.post(apiUrl + 'search_lt', {take_stock_parent_no:_self.take_stock_parent_no});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.take_stock_detail_status = _self.apply.take_stock_detail_status;
            $('#take_stock_detail_status').val(_self.apply.take_stock_detail_status).trigger('change');
            common.initSelect2($('#take_stock_flag'), _self.pagePara.TAKES_STOCK_FLAG);
            common.initSelect2($('#scrap_flag'), _self.pagePara.scraptype);
            common.initDatepicker($('#repair_plan_time'));

            initFixedAssetsDataTable();
            initAssetPurchaseTable();
            initAssetsAcceptanceTable();
            common.reSizeCall();
        }
        //固定资产
        function queryParamsAll(params) {
            params.search_text=$('#search_text_AIUDO').val();
            params.take_stock_detail_id=common.getUrlParams('take_stock_detail_id');
            params.scrap_type=1;
            return JSON.stringify(params)
        }

        function initFixedAssetsDataTable() {
            let columsArr = []
            console.log(_self.take_stock_detail_status)
            if (_self.take_stock_detail_status == 2){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormat('fixedassets_model', '归属部门编号'),
                    common.BTRowFormatAlignLeft('fixedassets_unit', '归属部门名称'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatWithFormatterAlignLeft('take_stock_description', '盘亏说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            } else {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormat('fixedassets_model', '归属部门编号'),
                    common.BTRowFormatAlignLeft('fixedassets_unit', '归属部门名称'),
                    common.BTRowFormatEdSelect2(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatEditable('take_stock_description', '盘亏说明'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            }
            $fixedAssetsInventoryDataTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchAsset',
                queryParams: queryParamsAll,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
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
                onEditableSave: async function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modifyAsset', row, 'fixedassetscheck_id', $('#fixedAssetsInventoryDataTable'));
                    $('#fixedAssetsInventoryDataTable').bootstrapTable("refresh")
                }
            });
            common.changeTableClass($fixedAssetsInventoryDataTable)
        }
        //待摊资产
        function queryParamsNet(params) {
            params.search_text=$('#search_text_AIUDT').val();
            params.take_stock_detail_id=common.getUrlParams('take_stock_detail_id');
            params.scrap_type=2;
            return JSON.stringify(params)
        }
        function initAssetPurchaseTable() {
            let columsArr = []
            console.log(_self.take_stock_detail_status)
            if (_self.take_stock_detail_status == 2){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatWithFormatterAlignLeft('take_stock_description', '盘亏说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            } else {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatEdSelect2(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatEditable('take_stock_description', '盘亏说明'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            }
            $deferredAssetsInventoryDataTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchAsset',
                queryParams: queryParamsNet,
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
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: async function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modifyAsset', row, 'amortize_id', $('#deferredAssetsInventoryDataTable'));
                    $('#deferredAssetsInventoryDataTable').bootstrapTable("refresh")
                }
            });
            common.changeTableClass($deferredAssetsInventoryDataTable)
        }
        //低值易耗品资产
        function queryParamsApple(params) {
            params.search_text=$('#search_text_AIDOThree').val();
            params.take_stock_detail_id=common.getUrlParams('take_stock_detail_id');
            params.scrap_type=3;
            return JSON.stringify(params)
        }
        function initAssetsAcceptanceTable() {
            let columsArr = []
            console.log(_self.take_stock_detail_status)
            if (_self.take_stock_detail_status == 2){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品资产编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品资产名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormat('consumables_unit', '计量单位'),
                    common.BTRowFormat('fixedassets_model', '归属部门编号'),
                    common.BTRowFormatAlignLeft('fixedassets_unit', '归属部门名称'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatWithFormatterAlignLeft('take_stock_description', '盘亏说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            } else {
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品资产编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品资产名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormat('consumables_unit', '计量单位'),
                    common.BTRowFormat('fixedassets_model', '归属部门编号'),
                    common.BTRowFormatAlignLeft('fixedassets_unit', '归属部门名称'),
                    common.BTRowFormatEdSelect2(_self, 'take_stock_flag', '盘亏状态', 'TAKES_STOCK_FLAG'),
                    common.BTRowFormatEditable('take_stock_description', '盘亏说明'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                ];
            }
            $valueConsumablesAssetsInventoryDataTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchAsset',
                queryParams: queryParamsApple,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'consumables_detail_id',
                uniqueId: 'consumables_detail_id',
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
                onEditableSave: async function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modifyAsset', row, 'consumables_detail_id', $('#valueConsumablesAssetsInventoryDataTable'));
                    $('#valueConsumablesAssetsInventoryDataTable').bootstrapTable("refresh")
                }
            });
            common.changeTableClass($valueConsumablesAssetsInventoryDataTable)
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
                $('#fixedAssetsInventoryDataTable').bootstrapTable("refresh")
            } else if (index === 2 ){
                $('#deferredAssetsInventoryDataTable').bootstrapTable("refresh")
            } else if (index === 3){
                $('#valueConsumablesAssetsInventoryDataTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#fixedAssetsInventoryDataTable').bootstrapTable("refresh")
            } else if (_self.tableType === 2 ){
                $('#deferredAssetsInventoryDataTable').bootstrapTable("refresh")
            } else if (_self.tableType === 3){
                $('#valueConsumablesAssetsInventoryDataTable').bootstrapTable("refresh")
            }
        },
        // addFixedAssets: function(event) {
        //     let _self = this;
        //     if (_self.take_stock_detail_status==2){
        //         return common.dealPromptCommon('该申请单已通过')
        //     }
        //     let params={
        //         take_stock_no: common.getUrlParams('take_stock_parent_no')
        //     }
        //     _self.$http.post(apiUrl + 'confirm', params).then((response) => {
        //         let retData = response.data.info;
        //         $('#tableFASDC').bootstrapTable("refresh");
        //         _self.searchedRow=[];
        //         common.dealSuccessCommon('提交成功');
        //         window.history.back()
        //     }, (response) => {
        //         common.dealErrorCommon(_self, response)
        //     });
        // }
    }
}
</script>
<style scoped>
</style>
