<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">

            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">资产报废申请单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产报废申请单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_FixedAOARO" placeholder="搜索申请单编号、申请人" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button class="btn btn-info" v-on:click="addFixedAssetsOrderAR" >-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 新增固定资产报废申请-->
                                    <!--</button>-->
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_DeferredAOARO" placeholder="搜索申请单编号、申请人" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button id="addAPO" class="btn btn-info" v-on:click="addDeferredAssetsOrderAR">-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 新增待摊资产报废申请-->
                                    <!--</button>-->
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==3">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_ValueCARO" placeholder="搜索申请单编号、申请人" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <!--<button id="addM1234" class="btn btn-info" v-on:click="addValueConsumablesAR">-->
                                        <!--<i class="glyphicon glyphicon-plus"></i> 新增低值易耗品报废申请-->
                                    <!--</button>-->
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#fixedAssetsDataARO" data-toggle="tab">
                                    <span>固定资产</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#deferredAssetsO" data-toggle="tab">
                                    <span>待摊资产</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#valueConsumablesO" data-toggle="tab">
                                    <span>低值易耗品</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fixedAssetsDataARO">
                                <table id="fixedAssetsDataAROTable"></table>
                            </div>
                            <div class="tab-pane fade" id="deferredAssetsO">
                                <table id="deferredAssetsOTable"></table>
                            </div>
                            <div class="tab-pane fade" id="valueConsumablesO">
                                <table id="valueConsumablesOTable"></table>
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
const apiUrl = '/api/nca/longtermassets/NCAAssetRetirementControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            workRow:{},
            tableType:1,
            index: ''
        }
    },
    name: 'NCAAssetRetirementControl',
    mounted: function() {
        let _self = this;
        let $fixedAssetsDataAROTable = $('#fixedAssetsDataAROTable');
        let $deferredAssetsOTable = $('#deferredAssetsOTable');
        let $valueConsumablesOTable = $('#valueConsumablesOTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            common.initSelect2($('#fixedassets_category'), _self.pagePara.fixedassetstype);
            common.initSelect2($('#depreciation_category'), _self.pagePara.depreciationmethodtype);
            common.initSelect2($('#purch_state'), _self.pagePara.purchstate);
            common.initSelect2($('#scrap_flag'), _self.pagePara.scraptype);

            initFixedAssetsDataTable();
            initDeferredAssetsTable();
            initValueConsumablesTable();
            common.reSizeCall();
        }
        //固定资产
        function nameFormatterF(value, row) {
            return [
                '<a class="show_detail_fixed" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsAll(params) {
            params.search_text=$('#search_text_FixedAOARO').val();
            params.scrap_type=1;
            return JSON.stringify(params)
        }

        function initFixedAssetsDataTable() {
            window.tableEvents = {
                'click .show_detail_fixed': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAFixedAssetsScrapDetailControl',
                        query: {
                            longassetsscrap_id: row.longassetsscrap_id,
                            longassetsscrap_no: row.longassetsscrap_no
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('longassetsscrap_no', '资产报废申请单编号', nameFormatterF, tableEvents),
                common.BTRowFormatAlignLeft('user_name', '申请人'),
                common.BTRowFormat('created_at', '申请时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'scrap_state', '状态', 'purchstate'),
                common.BTRowFormatAlignLeft('scrap_checker_name', '审批人'),
                common.BTRowFormat('scrap_check_date', '审批时间'),
                common.BTRowFormatWithFormatterAlignLeft('scrap_refuse_remark', '驳回说明',common.remarkFormatter)
            ];
            $fixedAssetsDataAROTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_s',
                queryParams: queryParamsAll,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'longassetsscrap_id',
                uniqueId: 'longassetsscrap_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($fixedAssetsDataAROTable)
        }
        //待摊资产
        function nameFormatterD(value, row) {
            return [
                '<a class="show_detail_deferred" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsNet(params) {
            params.search_text=$('#search_text_DeferredAOARO').val();
            params.scrap_type=2;
            return JSON.stringify(params)
        }
        function initDeferredAssetsTable() {
            window.tableEvents = {
                'click .show_detail_deferred': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCADeferredAssetsScrapDetailControl',
                        query: {
                            longassetsscrap_id: row.longassetsscrap_id,
                            longassetsscrap_no: row.longassetsscrap_no
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('longassetsscrap_no', '资产报废申请单编号', nameFormatterD, tableEvents),
                common.BTRowFormatAlignLeft('user_name', '申请人'),
                common.BTRowFormat('created_at', '申请时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'scrap_state', '状态', 'purchstate'),
                common.BTRowFormatAlignLeft('scrap_checker_name', '审批人'),
                common.BTRowFormat('scrap_check_date', '审批时间'),
                common.BTRowFormatWithFormatterAlignLeft('scrap_refuse_remark', '驳回说明',common.remarkFormatter)
            ];
            $deferredAssetsOTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_s',
                queryParams: queryParamsNet,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'longassetsscrap_id',
                uniqueId: 'longassetsscrap_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($deferredAssetsOTable)
        }
        //低值易耗品
        function purchaseApplyState(value, row) {
            for (let i = 0; i < _self.pagePara.purchstate.length; i++) {
                if (_self.pagePara.purchstate[i].id == value) {
                    return _self.pagePara.purchstate[i].text
                }
            }
            return ''
        }
        function nameFormatterV(value, row) {
            return [
                '<a class="show_detail_value" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsApple(params) {
            params.search_text=$('#search_text_ValueCARO').val();
            params.scrap_type=3;
            return JSON.stringify(params)
        }
        function initValueConsumablesTable() {
            window.tableEvents = {
                'click .show_detail_value': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAValueConsumablesScrapDetailControl',
                        query: {
                            longassetsscrap_id: row.longassetsscrap_id,
                            longassetsscrap_no: row.longassetsscrap_no
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('longassetsscrap_no', '资产报废申请单编号', nameFormatterV, tableEvents),
                common.BTRowFormatAlignLeft('user_name', '申请人'),
                common.BTRowFormat('created_at', '申请时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'scrap_state', '状态', 'purchstate'),
                common.BTRowFormatAlignLeft('scrap_checker_name', '审批人'),
                common.BTRowFormat('scrap_check_date', '审批时间'),
                common.BTRowFormatWithFormatterAlignLeft('scrap_refuse_remark', '驳回说明',common.remarkFormatter)
            ];
            $valueConsumablesOTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_s',
                queryParams: queryParamsApple,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'longassetsscrap_id',
                uniqueId: 'longassetsscrap_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($valueConsumablesOTable)
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
                $('#fixedAssetsDataAROTable').bootstrapTable("refresh")
            } else if (index === 2 ){
                $('#deferredAssetsOTable').bootstrapTable("refresh")
            } else if (index === 3){
                $('#valueConsumablesOTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#fixedAssetsDataAROTable').bootstrapTable("refresh")
            } else if (_self.tableType === 2 ){
                $('#deferredAssetsOTable').bootstrapTable("refresh")
            } else if (_self.tableType === 3){
                $('#valueConsumablesOTable').bootstrapTable("refresh")
            }
        },
        // addFixedAssetsOrderAR:async function () {
        //     let _self = this;
        //     let params={apply_approver:'',
        //                 scrap_type: 1};
        //     common.dealConfrimCommon('确认新增固定资产报废申请单？', async () => {
        //         _self.$http.post(apiUrl + 'add_s', params).then(response => {
        //             let longassetsscrap_id = response.body.info.longassetsscrap_id;
        //             let longassetsscrap_no = response.body.info.longassetsscrap_no;
        //             _self.$router.push({
        //                 path: '/nca/longtermassets/NCAFixedAssetsScrapDetailControl',
        //                 query: {
        //                     longassetsscrap_id: longassetsscrap_id,
        //                     longassetsscrap_no: longassetsscrap_no,
        //                     scrap_type: 1
        //                 }
        //             })
        //             $('#fixedAssetsDataAROTable').bootstrapTable("refresh");
        //         }, (response) => {
        //             common.dealErrorCommon(this, response);
        //         });
        //     });
        // },
        // addDeferredAssetsOrderAR: function(event) {
        //     let _self = this;
        //     let params={apply_approver:'',
        //                 scrap_type: 2};
        //     common.dealConfrimCommon('确认新增待摊资产报废申请单？', async () => {
        //         _self.$http.post(apiUrl + 'add_s', params).then(response => {
        //             let longassetsscrap_id = response.body.info.longassetsscrap_id;
        //             let longassetsscrap_no = response.body.info.longassetsscrap_no;
        //             _self.$router.push({
        //                 path: '/nca/longtermassets/NCADeferredAssetsScrapDetailControl',
        //                 query: {
        //                     longassetsscrap_id: longassetsscrap_id,
        //                     longassetsscrap_no: longassetsscrap_no,
        //                     scrap_type: 2
        //                 }
        //             })
        //             $('#deferredAssetsOTable').bootstrapTable("refresh");
        //         }, (response) => {
        //             common.dealErrorCommon(this, response);
        //         });
        //     });
        // },
        // addValueConsumablesAR:async function () {
        //     let _self = this;
        //     let params={apply_approver:'',
        //                 scrap_type: 3};
        //     common.dealConfrimCommon('确认新增低值易耗品报废申请单？', async () => {
        //         _self.$http.post(apiUrl + 'add_s', params).then(response => {
        //             let longassetsscrap_id = response.body.info.longassetsscrap_id;
        //             let longassetsscrap_no = response.body.info.longassetsscrap_no;
        //             _self.$router.push({
        //                 path: '/nca/longtermassets/NCAValueConsumablesScrapDetailControl',
        //                 query: {
        //                     longassetsscrap_id: longassetsscrap_id,
        //                     longassetsscrap_no: longassetsscrap_no,
        //                     scrap_type: 3
        //                 }
        //             })
        //             $('#valueConsumablesOTable').bootstrapTable("refresh");
        //         }, (response) => {
        //             common.dealErrorCommon(this, response);
        //         });
        //     });
        // }

    }
}
</script>
<style scoped>
</style>
