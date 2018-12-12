<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">待摊资产管理</a></li>
            <li class="active">待摊资产项目详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading panel-height">
                        <h4 class="panel-title pull-left">待摊资产项目详情</h4>
                        <!--<h4 class="panel-title pull-right ">项目名称：{{amortizeInfo.amortize_name}}</h4>-->
                        <!--<h4 class="panel-title pull-right margin-left">项目编号：{{amortizeInfo.amortize_code}}</h4>-->
                        <a class="btn btn-info btn-xs pull-right" style="margin-right: 30px;" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <!--材料申购-->
                            <div class="form-inline" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_subscribe" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="showTable(2)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                            <!--材料收料-->
                            <div class="form-inline" v-show="tableType==3" >
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_receive" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchReceive" class="btn btn-info" v-on:click="showTable(3)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                            <!--材料采购-->
                            <div class="form-inline" v-show="tableType==8" >
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_purchase" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="showTable(8)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                            <!--人工结算-->
                            <div class="form-inline" v-show="tableType==4">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_clear" placeholder="搜索施工名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="searchClear" class="btn btn-info" v-on:click="showTable(4)"><i class="fa fa-search"></i></button>
                                    </div>
                                </div>
                            </div>
                            <!--材料耗用-->
                            <div class="form-inline" v-show="tableType==5">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_consume" placeholder="搜索物料名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_consume" class="btn btn-info" v-on:click="showTable(5)"><i class="fa fa-search"></i></button>
                                    </div>
                                </div>
                            </div>
                            <!--构建费用-->
                            <div class="form-inline" v-show="tableType==6">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_cost" placeholder="搜索购建费用事项名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_cost" class="btn btn-info" v-on:click="showTable(6)"><i class="fa fa-search"></i></button>
                                    </div>
                                </div>
                            </div>
                            <!--移交验收-->
                            <div class="form-inline" v-show="tableType==7">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_check" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search_check" class="btn btn-info" v-on:click="showTable(7)"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#budget" data-toggle="tab">
                                    <span>购建预算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#subscribe" data-toggle="tab">
                                    <span>材料申购</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(8)">
                                <a href="#purchase" data-toggle="tab">
                                    <span>材料采购</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#amortizereceive" data-toggle="tab">
                                    <span>材料收料</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#amortizeclear" data-toggle="tab">
                                    <span>人工结算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(5)">
                                <a href="#amortizeconsume" data-toggle="tab">
                                    <span>材料耗用</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(6)">
                                <a href="#amortizecost" data-toggle="tab">
                                    <span>购建费用</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(7)">
                                <a href="#check_transfer" data-toggle="tab">
                                    <span>验收移交</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content" >
                            <div class="tab-pane fade active in" id="budget">
                                <table id="budgetTable"></table>
                            </div>
                            <div class="tab-pane fade" id="subscribe">
                                <table id="subscribeTable"></table>
                            </div>
                            <div class="tab-pane fade" id="purchase">
                                <table id="purchaseTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizereceive">
                                <table id="amortizeReceiveTable"></table>
                            </div>
                            <div class="tab-pane fade" id="amortizeclear">
                                <table id="amortizeclearTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizeconsume">
                                <table id="amortizeConsumeTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizecost">
                                <table id="amortizecostTable"></table>
                            </div>

                            <div class="tab-pane fade" id="check_transfer">
                                <div class="panel panel-inverse">
                                    <div class="panel panel-inverse">
                                        <div class="panel-heading panel-height">
                                            <h4 class="panel-title pull-left">决算管理</h4>
                                        </div>
                                    </div>
                                    <div class="panel-body">
                                        <div class="row">
                                            <div class="form-group col-sm-3">
                                                <div class="input-group">
                                                    <span class="input-group-addon">人工决算</span>
                                                    <div>
                                                        <input class="form-control" id="totalClearingLastRealityMoney" v-model="checkInfo.totalClearingLastRealityMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group">
                                                    <span class="input-group-addon">材料决算</span>
                                                    <div>
                                                        <input class="form-control" id="totalConsumeMoney" v-model="checkInfo.totalConsumeMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group ">
                                                    <span class="input-group-addon">其他费用</span>
                                                    <div>
                                                        <input class="form-control" id="totalAmortizeCostMoney" v-model="checkInfo.totalAmortizeCostMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group ">
                                                    <span class="input-group-addon">费用合计</span>
                                                    <div>
                                                        <input class="form-control" id="totalMoney" v-model="checkInfo.totalMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <div class="panel panel-inverse">
                                    <div class="panel-heading panel-height">
                                        <h4 class="panel-title pull-left">材料结余</h4>
                                    </div>
                                </div>
                                <table id="materialBalanceTable"></table>
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
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeDetailControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                workRow:{},
                searchedRow:[],
                amortizeInfo:[],
                amortize_id: '',
                oldRow:{},
                index: '',

                subscribeState:'',
                consumeState:'',
                costState:'',
                clearState:'',
                checkState:'',
                budgetState:'',

                checkInfo:'',
                budgetTotal:{},
                costTotal:{},
                show_return: 0
            }
        },
        name: 'NCAAmortizeDetailControl',
        mounted: function() {
            let _self = this;

            _self.amortize_id = _self.$route.query.amortize_id;
            let $budgetTable = $('#budgetTable');
            let $subscribeTable = $('#subscribeTable');
            let $amortizeReceiveTable = $('#amortizeReceiveTable');
            let $amortizeConsumeTable = $('#amortizeConsumeTable');
            let $amortizecostTable = $('#amortizecostTable');
            let $amortizeclearTable = $('#amortizeclearTable');
            let $materialBalanceTable =$('#materialBalanceTable');
            let $purchaseTable =$('#purchaseTable');

            //购建预算
            function queryParamsBudget(params) {
                params.amortize_id=_self.amortize_id;
                return JSON.stringify(params)
            }
            function initBudgetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormat('budget_measurement', '计量单位'),
                    common.BTRowFormatAlignRight('budget_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_price', '人工预算单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_materiel_price', '材料预算单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_money', '人工预算总价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_materiel_money', '材料预算总价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_total_money', '总金额',common.priceFormat),
                ];
                $budgetTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchBudget',
                    queryParams: queryParamsBudget,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        // _self.budgetState = stateFilter(res.info.rows,_self.pagePara.amortizeBudgetState,'budget_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizebudget_id',
                    uniqueId: 'amortizebudget_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($budgetTable)
            }

            //材料申购
            function queryParamsSubscribe(params) {
                params.amortize_id=_self.amortize_id;
                params.search_text=$('#search_text_subscribe').val();
                return JSON.stringify(params)
            }
            function initSubscribeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format', '规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribe_number', '数量'),
                    common.BTRowFormatAlignLeft('subscribe_remark', '备注',common.remarkFormatter),
                ];
                $subscribeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsSubscribe,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        // _self.subscribeState = stateFilter(res.info.rows,_self.pagePara.amortizeSubscribeState,'subscribe_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($subscribeTable)
            }

            //材料采购
            function queryParamsPurchase(params) {
                params.amortize_id=_self.amortize_id;
                params.search_text=$('#search_text_purchase').val();
                return JSON.stringify(params)
            }
            function initPurchaseTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribeorderdetailend_name', '物料名称'),
                    common.BTRowFormat('subscribeorderdetailend_format', '规格型号'),
                    common.BTRowFormat('subscribeorderdetailend_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribeorderdetailend_number', '申购数量'),
                    common.BTRowFormatAlignRight('purchase_done_number', '已采购数量'),
                    common.BTRowFormatAlignRight('purchaseorderdetail_number', '本次采购数量'),
                    common.BTRowFormatWithFormatterAlignRight('purchaseorderdetail_price', '单价',common.priceFormat),
                ];
                $purchaseTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubcribeOrderDetailEnd',
                    queryParams: queryParamsPurchase,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizepurchaseorder_id',
                    uniqueId: 'amortizepurchaseorder_id',
                    striped: true,
                    clickToSelect: false,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($purchaseTable)
            }

            //材料收料
            function queryParamsReceive(params) {
                params.search_text=$('#search_text_receive').val();
                params.amortize_id = _self.amortize_id;
                return JSON.stringify(params)
            }
            function initReceiveTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribe_number', '申购数量'),
                    common.BTRowFormatAlignRight('receive_done_number', '已收料数量'),
                    common.BTRowFormatAlignLeft('receivesupplier_name', '供应商名称'),
                    common.BTRowFormatAlignRight('receive_now_number', '本次收料数量'),
                    common.BTRowFormatWithFormatterAlignRight('receive_now_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('receive_now_money', '金额',common.priceFormat),
                ];
                $amortizeReceiveTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsReceive,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
                    striped: true,
                    clickToSelect: false,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($amortizeReceiveTable)
            }

            // 人工结算
            function queryParamsClear(params) {
                params.amortize_id=_self.amortize_id;
                params.search_text=$('#search_text_clear').val();
                return JSON.stringify(params)
            }
            function initClearTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormat('budget_measurement', '计量单位'),
                    common.BTRowFormatAlignRight('budget_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_price', '人工预算单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_money', '人工预算总价',common.priceFormat),
                    common.BTRowFormatAlignRight('clearing_last_finishlimit', '上次施工完成度'),
                    common.BTRowFormatAlignRight('clearing_now_finishlimit', '本次项目完成度'),
                    common.BTRowFormatWithFormatterAlignRight('clearing_estimate_money', '预计进度款',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('clearing_reality_money', '实际进度款',common.priceFormat),
                ];
                $amortizeclearTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchBudget',
                    queryParams: queryParamsClear,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        // _self.clearState = stateFilter(res.info.rows,_self.pagePara.amortizeClearingState,'clearing_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizebudget_id',
                    uniqueId: 'amortizebudget_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($amortizeclearTable)
            }

            //材料耗用
            function queryParamsConsume(params) {
                params.search_text=$('#search_text_consume').val();
                params.amortize_id = _self.amortize_id;
                return JSON.stringify(params)
            }
            function initConsumeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('residue_done_number', '现有数量'),
                    common.BTRowFormatAlignRight('consume_now_number', '本次耗用数量'),
                    common.BTRowFormatWithFormatterAlignRight('consume_now_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('consume_now_money', '金额',common.priceFormat),
                ];
                $amortizeConsumeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsConsume,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        // _self.consumeState = stateFilter(res.info.rows,_self.pagePara.amortizeConsumeState,'consume_state')
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($amortizeConsumeTable)
            }

            // 购建费用
            function queryParamsCost(params) {
                params.amortize_id = _self.amortize_id;
                params.search_text=$('#search_text_cost').val();
                return JSON.stringify(params)
            }
            function initCostTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('amortizecost_name', '购建费用事项'),
                    common.BTRowFormatWithFormatterAlignRight('amortizecost_invoice_money', '有发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('amortizecost_noinvoice_money', '无发票金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('amortizecost_money','金额',common.priceFormat),
                ];
                $amortizecostTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCost',
                    queryParams: queryParamsCost,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        // _self.costState = stateFilter(res.info.rows,_self.pagePara.amortizeCostState,'amortizecost_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizecost_id',
                    uniqueId: 'amortizecost_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($amortizecostTable)
            }

            // 验收移交 材料结余
            function queryParamsTranfer(params) {
                params.amortize_iwd = _self.amortize_id;
                params.search_text = $('#search_text_check').val();
                return JSON.stringify(params)
            }
            function initCheckTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('surplus_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('surplus_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('surplus_money', '金额',common.priceFormat),
                ];
                $materialBalanceTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCheck',
                    queryParams: queryParamsTranfer,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.checkInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($materialBalanceTable)
            }

            async function initData() {
                let response = {};

                response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                _self.show_return = _self.$route.query.show_return;

                response = await _self.$http.post(apiUrl + 'getAmortize', {amortize_id:_self.amortize_id});
                _self.amortizeInfo = response.data.info[0];
                console.log(response.data,'_self.amortizeInfo')

                response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                _self.budgetTotal = response.data.info;

            }
            $(function() {
                initData();
                initBudgetTable();
                initSubscribeTable();
                initPurchaseTable();
                initReceiveTable();
                initConsumeTable();
                initCostTable();
                initClearTable();
                initCheckTable();
            })
        },
        methods: {
            showTable:async function(index){
                let _self = this;
                _self.tableType=index;
                if(index === 1 ){
                    let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    _self.budgetTotal = response.data.info;
                    $('#budgetTable').bootstrapTable("refresh");
                } else if (index === 2 ){
                    $('#subscribeTable').bootstrapTable("refresh")
                } else if (index === 3){
                    $('#amortizeReceiveTable').bootstrapTable("refresh")
                } else if (index === 4){
                    let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    _self.budgetTotal = response.data.info;
                    $('#amortizeclearTable').bootstrapTable("refresh")
                } else if (index === 5){
                    $('#amortizeConsumeTable').bootstrapTable("refresh")
                }else if(index === 6){
                    let response = await _self.$http.post(apiUrl + 'getSumCostMoney', {amortize_id:_self.amortize_id});
                    _self.costTotal = response.data.info;
                    $('#amortizecostTable').bootstrapTable("refresh")
                }else if(index === 7){
                    $('#materialBalanceTable').bootstrapTable("refresh")
                }else {
                    $('#purchaseTable').bootstrapTable("refresh")
                }
            },
        }
    }
</script>
<style scoped>
    .panel-height{
        height: 3em;
    }
    .span1{
        font-size: 1.2em;
    }
    .margin-left{
        margin-right: 2em;
    }
    .padding-left{
        padding-left: 2em;
    }
</style>
