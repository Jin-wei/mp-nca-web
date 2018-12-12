<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">材料申购单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">材料申购单列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索申购单编号，项目编号，项目名称" style="width: 20em">
                                </div>
                            </div>
                            <!--<div class="form-group select-width">-->
                                <!--<select class="select2 form-control select-width" id="option_type">-->
                                    <!--<option value="1">待摊资产项目</option>-->
                                    <!--<option value="2">研发资产项目</option>-->
                                    <!--&lt;!&ndash;<option v-for="type in types" v-bind:value="type.id">{{type.text}}</option>&ndash;&gt;-->
                                <!--</select>-->
                            <!--</div>-->
                            <div class="form-group ">
                                <button id="search" class="btn btn-info"  v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="amortizeTable"></table>
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
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeScribeOrderControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                pagePara: {},
                state: 1,
            }
        },
        name: 'NCAAmortizeScribeOrderControl',
        mounted: function() {
            let _self = this;
            let $amortizeTable = $('#amortizeTable');
            // let $developTable = $('#developTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>',
                ].join('')
            }function actFormatter(value, row) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 tableDelete" style="cursor: pointer;">删除</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAmortizeScribeOrderDetailControl',
                        query: {
                            amortizesubscribeorder_id: row.amortizesubscribeorder_id,
                            // developsubscribeorder_id: row.developsubscribeorder_id
                        }
                    })
                },
                'click .tableDelete': function(e, value, row, index) {
                    if(row.subscribeorder_state === 3){
                        common.dealConfrimCommon('您确定删除吗？', function () {
                            // if(row.developsubscribeorder_id === undefined){
                                _self.$http.post(apiUrl + 'deleteAmortizeSubscribeOrder', {amortizesubscribeorder_id: row.amortizesubscribeorder_id}).then((response) => {
                                    $('#amortizeTable').bootstrapTable("refresh");
                                }, (response) => {
                                    return common.dealPromptCommon('删除失败');
                                });
                            // }else {}
                            // if(row.amortizesubscribeorder_id === undefined){
                            //     _self.$http.post(apiUrl + 'deleteDevelopSubscribeOrder', {developsubscribeorder_id: row.developsubscribeorder_id}).then((response) => {
                            //         $('#developTable').bootstrapTable("refresh");
                            //     }, (response) => {
                            //         return common.dealPromptCommon('删除失败');
                            //     });
                            // }else {}
                        });
                    }else{
                        common.dealPromptCommon('该申购单状态不是拒绝状态，不能删除该申购单')
                    }
                },
            };
            function queryParams(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initAmortizeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('subscribeorder_code', '申购单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormatAlignLeft('creatorName', '申购人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatAlignLeft('examineName', '审批人'),
                    common.BTRowFormat('subscribeorder_examine_time', '审批时间'),
                    common.BTRowFormatWithFormatterAlignLeft('subscribeorder_refuse_remark', '驳回说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self,'subscribeorder_state', '状态','amortizeSubscribeOrderState'),
                    common.BTRowFormatWithFE('', '操作',actFormatter,tableEvents),
                ];
                $amortizeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getAmortizeSubscribeOrder',
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
                common.changeTableClass($amortizeTable)
            }


            // function queryParams(params) {
            //     params.search_text=$('#search_text').val();
            //     return JSON.stringify(params)
            // }
            // function initDevelopTable() {
            //     let columsArr = [
            //         common.BTRowFormatWithFE('subscribeorder_code', '申购单编号', nameFormatter,tableEvents),
            //         common.BTRowFormat('develop_code', '项目编号'),
            //         common.BTRowFormat('develop_name', '项目名称'),
            //         common.BTRowFormat('budget_work_name', '施工项名称'),
            //         common.BTRowFormat('creatorName', '申购人'),
            //         common.BTRowFormat('created_at', '创建时间'),
            //         common.BTRowFormat('examineName', '审批人'),
            //         common.BTRowFormat('subscribeorder_examine_time', '审批时间'),
            //         common.BTRowFormat('subscribeorder_refuse_remark', '驳回说明'),
            //         common.BTRowFormatEdSelect2Disabled(_self,'subscribeorder_state', '状态','amortizeSubscribeOrderState'),
            //         common.BTRowFormatWithFE('', '操作',actFormatter,tableEvents),
            //     ];
            //     $developTable.bootstrapTable({
            //         method: 'POST',
            //         url: apiUrl + 'getDevelopSubscribeOrder',
            //         queryParams: queryParams,
            //         sidePagination: 'server',
            //         ajaxOptions: common.bootstrapTableAjaxOtions,
            //         responseHandler: function(res) {
            //             return res.info;
            //         },
            //         height: common.getTableHeight(),
            //         columns: columsArr,
            //         idField: 'developsubscribeorder_id',
            //         uniqueId: 'developsubscribeorder_id',
            //         striped: true,
            //         clickToSelect: true,
            //         showRefresh: false,
            //         pagination: true,
            //         pageSize: common.pageSize(),
            //         pageList: [10, 15, 25, 50, 100],
            //         locale: 'zh-CN',
            //
            //     });
            //     common.changeTableClass($developTable)
            // }
            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                initAmortizeTable();
                // initDevelopTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: async function(event) {
                // let _self = this;
                // _self.state = $("#option_type").val();
                // if(_self.state == 2){
                //     $('#developTable').bootstrapTable("refresh");
                // }
                // if(_self.state == 1){
                    $('#amortizeTable').bootstrapTable("refresh");

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
