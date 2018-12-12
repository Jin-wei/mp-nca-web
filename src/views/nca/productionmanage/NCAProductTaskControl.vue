<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产任务单列表</a></li>
            <!--<li class="active">材料申购单管理</li>-->
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">生产任务单列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索物料编码，物料名称" style="width: 20em">
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
                        <table id="productTable"></table>
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
    const apiUrl = '/api/nca/productionmanage/NCAProductTaskControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                pagePara: {},
                state: 1,
            }
        },
        name: 'NCAProductTaskControl',
        mounted: function() {
            let _self = this;
            let $productTable = $('#productTable');
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
            function initProductTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('subscribeorder_code', '生产任务单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('amortize_code', '物料编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '物料名称'),
                    common.BTRowFormat('budget_work_name', '规格型号'),
                    common.BTRowFormat('creatorName', '计量单位'),
                    common.BTRowFormatAlignRight('created_at', '数量'),
                    common.BTRowFormat('examineName', '订单号'),
                    common.BTRowFormatWithFE('', '操作',actFormatter,tableEvents),
                ];
                $productTable.bootstrapTable({
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
                common.changeTableClass($productTable)
            }


            async function initData() {
                // let response = await _self.$http.post(apiUrl + 'init', {});
                // let retData = response.data.info;
                // _self.pagePara = JSON.parse(JSON.stringify(retData));
                initProductTable();
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
