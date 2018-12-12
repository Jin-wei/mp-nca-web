<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">长期资产管理</a></li>
            <li class="active">资产管理申购单明细</li>
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
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="tab-content" style="padding: 15px">
            <table id="subscribeAmortizeTable"></table>
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
        name: 'NCAAmortizeScribeOrderControl',
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

            function queryAmortizeParams(params) {
                params.search_text=$('#search_text').val();
                params.amortizesubscribeorder_id = _self.amortizesubscribeorder_id;
                return JSON.stringify(params)
            }
            function initAmortizeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribeorderdetail_name', '物料名称'),
                    common.BTRowFormat('subscribeorderdetail_format', '规格型号'),
                    common.BTRowFormat('subscribeorderdetail_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribeorderdetail_number', '数量'),
                    common.BTRowFormatWithFormatterAlignLeft('subscribeorderdetail_remark', '备注',common.remarkFormatter),
                    ]
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

                });
                common.changeTableClass($subscribeAmortizeTable)
            }

            // function queryDevelopParams(params) {
            //     params.search_text=$('#search_text').val();
            //     params.developsubscribeorder_id = _self.developsubscribeorder_id;
            //     return JSON.stringify(params)
            // }
            // function initDevelopTable() {
            //     let columsArr = [
            //         common.BTRowFormatWithIndex('NO.'),
            //         common.BTRowFormat('subscribeorderdetail_name', '物料名称'),
            //         common.BTRowFormat('subscribeorderdetail_format', '规格型号'),
            //         common.BTRowFormat('subscribeorderdetail_unit', '计量单位'),
            //         common.BTRowFormat('subscribeorderdetail_number', '数量'),
            //         common.BTRowFormat('subscribeorderdetail_remark', '备注'),
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
        }
    }
</script>
<style scoped>
</style>
