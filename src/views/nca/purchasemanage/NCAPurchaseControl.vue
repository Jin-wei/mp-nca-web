<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">采购管理</a></li>
            <li class="active">采购列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <!--<DomainChooseHeader :pagePara="pagePara" :apiName="apiName"></DomainChooseHeader>-->
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">采购列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_all" placeholder="搜索物料编码,物料名称,销售订单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="materiel_source_kind_all"></select>
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="management_model_all"></select>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="beginTimeAll" placeholder="起始时间" />
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="endTimeAll" placeholder="结束时间" />
                                </div>
                                <div class="form-group" >
                                    <button id="searchAll" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>

                                    <div class="form-group">
                                        <input type="text" class="form-control" id="mrpDate" placeholder="mrp运算日期" />
                                    </div>
                                    <button id="testDemand" class="btn btn-info" v-on:click="dataExtract" >
                                        <i class="fa fa-search">数据汇总</i>
                                    </button>
                                    <button id="getAllDemand" class="btn btn-info" v-on:click="getAllDemand" >
                                        <i class="fa fa-search">总需求表</i>
                                    </button>
                                    <button id="getNetDemand" class="btn btn-info" v-on:click="getNetDemand" >
                                        <i class="fa fa-search">净需求表</i>
                                    </button>
                                    <button id="getProduction" class="btn btn-info" v-on:click="getProduction" >
                                        <i class="fa fa-search">生产</i>
                                    </button>
                                    <button id="getPurchase" class="btn btn-info" v-on:click="getPurchase" >
                                        <i class="fa fa-search">物料采购订单列表</i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_net" placeholder="搜索物料编码,物料名称,销售订单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="materiel_source_kind_net"></select>
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="management_model_net"></select>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="beginTimeNet" placeholder="起始时间" />
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="endTimeNet" placeholder="结束时间" />
                                </div>
                                <div class="form-group ">
                                    <button id="searchNet" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-line" v-show="tableType==3">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_apply" placeholder="搜索采购申请订单号" style="width: 230px;">
                                </div>
                                <div class="form-group ">
                                    <button id="searchApply" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>

                                </div>
                                <div class="form-group pull-right">
                                    <button id="addM1234" class="btn btn-info" v-on:click="addApprover" >增加
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==4">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_purchase" placeholder="搜索采购订单号" style="width: 230px;">
                                </div>
                                <div class="form-group ">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#allDemand" data-toggle="tab">
                                    <span>总需求</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#netDemand" data-toggle="tab">
                                    <span>净需求</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#purchaseApply" data-toggle="tab">
                                    <span>采购申请</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#purchase" data-toggle="tab">
                                    <span>物料采购订单列表</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="allDemand">
                                <table id="allDemandTable"></table>
                            </div>
                            <div class="tab-pane fade" id="netDemand" >
                                <table id="netDemandTable"></table>
                            </div>
                            <div class="tab-pane fade" id="purchaseApply">
                                <table id="purchaseApplyTable"></table>
                            </div>
                            <div class="tab-pane fade" id="purchase">
                                <table id="purchaseTable"></table>
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
    const apiUrl = '/api/nca/purchasemanage/NCAPurchaseControl?method=';
//    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
//                apiName: common.getApiName(apiUrl),
            }
        },
        name: 'NCAPurchaseControl',
//        components: {
//            DomainChooseHeader
//        },
        mounted: function() {
            let _self = this;
            let $allDemandTable = $('#allDemandTable');
            let $netDemandTable = $('#netDemandTable');
            let $purchaseApplyTable = $('#purchaseApplyTable');
            let $purchaseTable = $('#purchaseTable');

            async function initData() {
                common.initDatepicker($('#beginTimeAll'));
                common.initDatepicker($('#endTimeAll'));
                common.initDatepicker($('#beginTimeNet'));
                common.initDatepicker($('#endTimeNet'));
                common.initDatepicker($('#mrpDate'));

                let response = await _self.$http.post(apiUrl + 'initAct', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                common.initSelect2Placeholder($('#materiel_source_kind_all'), retData.materielSourceKind, '来源分类');
                common.initSelect2Placeholder($('#management_model_all'), retData.materielManage, '管理模式');
                common.initSelect2Placeholder($('#materiel_source_kind_net'), retData.materielSourceKind, '来源分类');
                common.initSelect2Placeholder($('#management_model_net'), retData.materielManage, '管理模式');

                common.initSelect2Placeholder($('#apply_state_all'), retData.purchaseApplyType, '状态');
                common.initSelect2Placeholder($('#approver'), retData.user, '');

                initAllDemandTable();
                initNetDemandTable();
                initPurchaseApplyTable();
                initPurchaseTable();
                common.reSizeCall();
            }
            //总需求
            function queryParamsAll(params) {
                params.search_text=$('#search_text_all').val();
                params.materiel_source=common.getSelect2Val('materiel_source_kind_all');
                params.materiel_manage=common.getSelect2Val('management_model_all');
                params.beginTime=$("#beginTimeAll").val();
                params.endTime=$("#endTimeAll").val();
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }

                return JSON.stringify(params)
            }
            function initAllDemandTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
//                    common.BTRowFormat('materiel_barcode', '物料条形码'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('demand_amount', '总需求数量'),
                    common.BTRowFormat('order_id', '销售单号'),
//                    common.BTRowFormat('materiel_batch', '物料批次'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '来源分类', 'materielSourceKind'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '管理模式', 'materielManage'),
                    common.BTRowFormat('mrp_date', 'MRP运算时间')
                ];
                $allDemandTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getAllDemand',
                    queryParams: queryParamsAll,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'alldemand_id',
                    uniqueId: 'alldemand_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($allDemandTable)
            }
            //净需求
            function queryParamsNet(params) {
                params.search_text=$('#search_text_net').val();
                params.materiel_source_kind=common.getSelect2Val('materiel_source_kind_net');
                params.materiel_manage=common.getSelect2Val('management_model_net');
                params.beginTime=$("#beginTimeNet").val();
                params.endTime=$("#endTimeNet").val();
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }
                return JSON.stringify(params)
            }
            function initNetDemandTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
//                    common.BTRowFormat('materiel_barcode', '物料条形码'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('netdemand_amount', '净需求数量'),
                    common.BTRowFormat('order_id', '销售单号'),
//                    common.BTRowFormat('materiel_batch', '物料批次'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '来源分类', 'materielSourceKind'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '管理模式', 'materielManage'),
                    common.BTRowFormat('mrp_date', 'MRP运算时间')
                ];
                $netDemandTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getNetDemand',
                    queryParams: queryParamsNet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'netdemand_id',
                    uniqueId: 'netdemand_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($netDemandTable)
            }
            //采购申请
            function purchaseApplyState(value, row) {
                for (let i = 0; i < _self.pagePara.purchaseApplyType.length; i++) {
                    if (_self.pagePara.purchaseApplyType[i].id == value) {
                        return _self.pagePara.purchaseApplyType[i].text
                    }
                }
                return ''
            }
            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer" title="查看">',
                    value,
                    '</a>'
                ].join('')
            }
            function queryParamsApple(params) {
                params.search_text=$('#search_text_apply').val();
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }
                return JSON.stringify(params)
            }
            function initPurchaseApplyTable() {
                window.tableEvents = {
                    'click .show_detail': function(e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/purchasemanage/NCAPurchaseApplyDetailControl',
                            query: {
                                purchaseapply_id: row.purchaseapply_id,
                                order_type: row.order_type
                            }
                        })
                    },
                    'click .delete_purchaseApply': function(e, value, row, index) {
                        if(row.apply_state==1){
                            return common.dealPromptCommon('该申请单已提交')
                        }else if (row.apply_state==2){
                            return common.dealPromptCommon('该申请单已通过')
                        }
                        common.rowDeleteWithApi(_self, '采购申请单删除', apiUrl + 'deletePurchaseApply', $purchaseApplyTable, row, 'purchaseapply_id', function() {})
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('purchaseapply_id', '采购申请单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('apply_applicant', '申请人'),
                    common.BTRowFormatWithFormatter('apply_state', '申请单状态', purchaseApplyState),
                    common.BTRowFormatAlignLeft('apply_approver', '审批人'),
                    common.BTRowFormat('approval_date', '审批时间'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatWithFormatterAlignLeft('description', '驳回说明',common.remarkFormatter),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 process delete_purchaseApply">删除</a>';
                    }, tableEvents)
                ];
                $purchaseApplyTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getPuchaseApply',
                    queryParams: queryParamsApple,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'purchase_id',
                    uniqueId: 'purchase_id',
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
                common.changeTableClass($purchaseApplyTable)
            }
            //物料采购
            function queryParamsPurchase(params) {
                params.search_text=$('#search_text_purchase').val();
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }
                return JSON.stringify(params)
            }
            function initPurchaseTable() {
                window.tableEvents = {
                    'click .show_detail': function(e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/purchasemanage/NCAPurchaseDetailControl',
                            query: {
                                purchaseorder_id: row.purchaseorder_id
                            }
                        })
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('purchaseorder_id', '采购单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('purchase_domain', '采购机构'),
                    common.BTRowFormatAlignLeft('order_domain', '销售机构'),
                    common.BTRowFormatAlignLeft('supplier_name', '供应商名称'),
                    common.BTRowFormat('created_at', '创建日期'),
                ];
                $purchaseTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getPurchaseOrder',
                    queryParams: queryParamsPurchase,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'purchaseorder_id',
                    uniqueId: 'purchaseorder_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($purchaseTable)
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
                    $('#allDemandTable').bootstrapTable("refresh")
                } else if (index === 2 ){
                    $('#netDemandTable').bootstrapTable("refresh")
                } else if (index === 3){
                    $('#purchaseApplyTable').bootstrapTable("refresh")
                }else if (index === 4){
                    $('#purchaseTable').bootstrapTable("refresh")
                }
            },
            queryConfirm: function(event) {
                let _self = this;
                if(_self.tableType === 1 ){
                    $('#allDemandTable').bootstrapTable("refresh")
                } else if (_self.tableType === 2 ){
                    $('#netDemandTable').bootstrapTable("refresh")
                } else if (_self.tableType === 3){
                    $('#purchaseApplyTable').bootstrapTable("refresh")
                } else if (_self.tableType === 4){
                    $('#purchaseTable').bootstrapTable("refresh")
                }
            },
            dataExtract:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'dataExtract', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            getAllDemand:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'setAllDemand', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            getNetDemand:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'setNetDemand', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            getProduction:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'setProduction', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            getPurchase:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'setPurchase', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },

            addApprover:async function () {
                let _self = this;
                let params={apply_approver:''};
                common.dealConfrimCommon('确认新增采购申请单？', async () => {
                    _self.$http.post(apiUrl + 'addPuchaseApply', params).then(response => {
                        $('#purchaseApplyTable').bootstrapTable("refresh");
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                });
            }
        }
    }
</script>
<style scoped>
</style>
