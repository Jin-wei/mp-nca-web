<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产任务单详情</a></li>
            <!--<li class="active">材料申购单管理</li>-->
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <!--<div class="panel-heading-btn">-->
                            <!--&lt;!&ndash;<a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>&ndash;&gt;-->
                        <!--</div>-->
                        <h4 class="panel-title">生产任务单详情</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" v-show="tableType == 1">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索生产车间，生产工序" style="width: 20em">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info"  v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>

                        <div class="form-inline" v-show="tableType == 2">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_workshop" placeholder="搜索物料编码，物料名称" style="width: 20em">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search_workshop" class="btn btn-info"  v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>

                    </div>
                    <div class="panel-body">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)" id="name1">
                                <a href="#workShop" data-toggle="tab">
                                    <span>车间及工序</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)" id="name">
                                <a href="#feedCommand" data-toggle="tab">
                                    <span>投料命令</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content" >
                            <div class="tab-pane fade active in" id="workShop">
                                <table id="workShopTable"></table>
                            </div>
                            <div class="tab-pane fade" id="feedCommand">
                                <table id="feedCommandTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <!--<div class="tab-content" v-show="state == 2 ">-->
        <!--<table id="developTable"></table>-->
        <!--</div>-->
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/productionmanage/NCAProductTaskDetailControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                pagePara: {},
                state: 1,
                tableType:1,
                index: '',
            }
        },
        name: 'NCAProductTaskDetailControl',
        mounted: function() {
            let _self = this;
            let $workShopTable = $('#workShopTable');
            let $feedCommandTable = $('#feedCommandTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>',
                ].join('')
            }
            function queryParams(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initWorkShopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribeorder_code', '生产车间'),
                    common.BTRowFormatAlignLeft('amortize_code', '工序优先级'),
                    common.BTRowFormatAlignLeft('amortize_name', '生产工序'),
                ];
                $workShopTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribeorder_id',
                    uniqueId: 'amortizesubscribeorder_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($workShopTable)
            }

            function queryParams(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initfeedCommandTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '物料编码'),
                    common.BTRowFormatAlignLeft('amortize_name', '物料名称'),
                    common.BTRowFormat('budget_work_name', '规格型号'),
                    common.BTRowFormat('creatorName', '计量单位'),
                    common.BTRowFormatAlignRight('created_at', '投料数量'),
                    common.BTRowFormatAlignLeft('examineName', '产品层级'),
                ];
                $feedCommandTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribeorder_id',
                    uniqueId: 'amortizesubscribeorder_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($feedCommandTable)
            }

            async function initData() {
                // let response = await _self.$http.post(apiUrl + 'init', {});
                // let retData = response.data.info;
                // _self.pagePara = JSON.parse(JSON.stringify(retData));
                initWorkShopTable();
                initfeedCommandTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            showTable:async function(index){
                // $("#subscribe").removeClass("active in")
                let _self = this;
                _self.tableType=index;
                if(index === 1 ){
                    // let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    // _self.budgetTotal = response.data.info;
                    $('#workShopTable').bootstrapTable("refresh");
                } else if (index === 2 ){
                    // let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    // _self.budgetTotal = response.data.info;
                    $('#feedCommandTable').bootstrapTable("refresh");
                }
            },
            queryConfirm: async function(event) {
                // let _self = this;
                // _self.state = $("#option_type").val();
                // if(_self.state == 2){
                //     $('#developTable').bootstrapTable("refresh");
                // }
                // if(_self.state == 1){
                $('#productTable').bootstrapTable("refresh");

                // }
            },
        }
    }
</script>
<style scoped>
    .span1{
        font-size: 1.2em;
    }
    .padding-left{
        padding-left: 2em;
    }
    .select-width {
        width: 180px;
    }
</style>
