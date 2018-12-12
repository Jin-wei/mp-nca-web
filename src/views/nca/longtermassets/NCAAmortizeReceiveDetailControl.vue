<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">长期资产管理</a></li>
            <li class="active">收料单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收料单</h4>
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
                    <div class="tab-content" style="padding: 15px">
                        <table id="receiveAmortizeDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <!--<div class="tab-content" v-show="state === 2">-->
            <!--<table id="receiveDevelopDetailTable"></table>-->
        <!--</div>-->
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeReceiveControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                amortizereceive_id:"",
                developreceive_id:"",
                state:''
            }
        },
        name: 'NCAAmortizeReceiveDetailControl',
        mounted: function() {
            let _self = this;
            _self.amortizereceive_id = _self.$route.query.amortizereceive_id
            // _self.developreceive_id = _self.$route.query.developreceive_id
            let $receiveAmortizeDetailTable = $('#receiveAmortizeDetailTable');
            // let $receiveDevelopDetailTable = $('#receiveDevelopDetailTable');
            // if(_self.amortizereceive_id === undefined){
            //     _self.state = 2
            // }
            // if(_self.developreceive_id === undefined){
            //     _self.state = 1
            // }

            function queryAmortizeParams(params) {
                params.search_text=$('#search_text').val();
                params.amortizereceive_id=_self.amortizereceive_id;

                return JSON.stringify(params)
            }
            function initAmortizeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format', '规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignLeft('receivesupplier_name', '供应商名称'),
                    common.BTRowFormatAlignRight('receivedetail_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('receivedetail_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('receivedetail_money', '金额',common.priceFormat)
                ];
                $receiveAmortizeDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchReceiveDetail',
                    queryParams: queryAmortizeParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizereceivedetail_id',
                    uniqueId: 'amortizereceivedetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($receiveAmortizeDetailTable)
            }

            // function queryDevelopParams(params) {
            //     params.search_text=$('#search_text').val();
            //     params.developreceive_id=_self.developreceive_id;
            //
            //     return JSON.stringify(params)
            // }
            // function initDevelopTable() {
            //     let columsArr = [
            //         common.BTRowFormat('subscribe_name', '物料名称'),
            //         common.BTRowFormat('subscribe_format', '规格型号'),
            //         common.BTRowFormat('subscribe_unit', '计量单位'),
            //         common.BTRowFormat('receivesupplier_name', '供应商名称'),
            //         common.BTRowFormat('receivedetail_number', '数量'),
            //         common.BTRowFormat('receivedetail_price', '单价'),
            //         common.BTRowFormat('receivedetail_money', '金额')
            //     ];
            //     $receiveDevelopDetailTable.bootstrapTable({
            //         method: 'POST',
            //         url: apiUrl + 'searchDevelopReceiveDetail',
            //         queryParams: queryDevelopParams,
            //         sidePagination: 'server',
            //         ajaxOptions: common.bootstrapTableAjaxOtions,
            //         responseHandler: function(res) {
            //             return res.info;
            //         },
            //         height: common.getTableHeight(),
            //         columns: columsArr,
            //         idField: 'developreceivedetail_id',
            //         uniqueId: 'developreceivedetail_id',
            //         striped: true,
            //         clickToSelect: true,
            //         showRefresh: false,
            //         pagination: true,
            //         pageSize: common.pageSize(),
            //         pageList: [10, 15, 25, 50, 100],
            //         locale: 'zh-CN',
            //
            //     });
            //     common.changeTableClass($receiveDevelopDetailTable)
            // }
            $(function() {
                initAmortizeTable();
                // initDevelopTable();

                common.reSizeCall();
            })
        },
        methods: {
            queryConfirm: function (event) {
                // $('#receiveDevelopDetailTable').bootstrapTable("refresh");
                $('#receiveAmortizeDetailTable').bootstrapTable("refresh");
            }
        }

    }
</script>
<style scoped>
</style>
