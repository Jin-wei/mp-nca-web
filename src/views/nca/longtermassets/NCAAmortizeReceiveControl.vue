<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">资产物料收料单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产物料收料单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索收料单编号">
                                </div>
                            </div>
                            <!--<div class="form-group select-width">-->
                                <!--<select class="select2 form-control select-width" id="option_type">-->
                                    <!--<option value='1'>待摊资产项目</option>-->
                                    <!--<option value='2'>研发资产项目</option>-->
                                <!--</select>-->
                            <!--</div>-->
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="receiveAmortizeTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <!--<div class="tab-content" v-show="state == 2">-->
            <!--<table id="receiveDevelopTable"></table>-->
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
                state: 1,
            }
        },
        name: 'NCAAmortizeReceiveControl',
        mounted: function() {
            let _self = this;
            let $receiveAmortizeTable = $('#receiveAmortizeTable');
            // let $receiveDevelopTable = $('#receiveDevelopTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAmortizeReceiveDetailControl',
                        query: {
                            amortizereceive_id: row.amortizereceive_id,
                            // developreceive_id: row.developreceive_id
                        }
                    })
                }
            };
            function queryParams(params) {
                params.search_text=$('#search_text').val();
                params.amortizereceive_id=_self.amortizereceive_id;
                return JSON.stringify(params)
            }
            function initAmortizeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('receive_code', '收料单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatAlignLeft('name', '收料人'),
                    common.BTRowFormat('created_at', '收料时间')
                ];
                $receiveAmortizeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchReceive',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.optionInfo = res.info.optionInfo;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizereceive_id',
                    uniqueId: 'amortizereceive_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($receiveAmortizeTable)
            }

            // function queryParams(params) {
            //     params.search_text=$('#search_text').val();
            //     params.developreceive_id=_self.developreceive_id;
            //     return JSON.stringify(params)
            // }
            // function initDevelopTable() {
            //     let columsArr = [
            //         common.BTRowFormatWithFE('receive_code', '收料单编号', nameFormatter,tableEvents),
            //         common.BTRowFormat('develop_code', '项目编号'),
            //         common.BTRowFormat('develop_name', '项目名称'),
            //         common.BTRowFormat('name', '收料人'),
            //         common.BTRowFormat('created_at', '收料时间')
            //     ];
            //     $receiveDevelopTable.bootstrapTable({
            //         method: 'POST',
            //         url: apiUrl + 'searchDevelopReceive',
            //         queryParams: queryParams,
            //         sidePagination: 'server',
            //         ajaxOptions: common.bootstrapTableAjaxOtions,
            //         responseHandler: function(res) {
            //             _self.optionInfo = res.info.optionInfo;
            //             return res.info;
            //         },
            //         height: common.getTableHeight(),
            //         columns: columsArr,
            //         idField: 'developreceive_id',
            //         uniqueId: 'developreceive_id',
            //         striped: true,
            //         clickToSelect: true,
            //         showRefresh: false,
            //         pagination: true,
            //         pageSize: common.pageSize(),
            //         pageList: [10, 15, 25, 50, 100],
            //         locale: 'zh-CN',
            //
            //     });
            //     common.changeTableClass($receiveDevelopTable)
            // }

            $(async function() {
                // initDevelopTable();
                initAmortizeTable();
                // common.initSelect2($('#option_type'),  _self.optionInfo);
                common.reSizeCall();
            })
        },
        methods: {
            queryConfirm: function(event) {
                // let _self = this;
                // _self.state = $('#option_type').val();
                // if(_self.state == 2){
                //     $('#receiveDevelopTable').bootstrapTable("refresh");
                // }
                // if(_self.state == 1){
                    $('#receiveAmortizeTable').bootstrapTable("refresh");
                // }
            }
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
