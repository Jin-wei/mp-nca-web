<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">研发资产管理</a></li>
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
                            <div class="form-group ">
                                <button id="search" class="btn btn-info"  v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <table id="developTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCADevelopScribeOrderControl?method=';
    export default {
        data: function() {
            return {
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                pagePara: {},
                state: 1,
            }
        },
        name: 'NCADevelopScribeOrderControl',
        mounted: function() {
            let _self = this;
            let $developTable = $('#developTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>',
                ].join('')
            }function actFormatter(value, row) {
                return [
                    '<a class="btn btn-xs btn-info m-r-5 tableDelete" style="cursor: pointer;">删除</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCADevelopScribeOrderDetailControl',
                        query: {
                            developsubscribeorder_id: row.developsubscribeorder_id
                        }
                    })
                },
                'click .tableDelete': function(e, value, row, index) {
                    if(row.subscribeorder_state === 3){
                        common.dealConfrimCommon('您确定删除吗？', function () {
                                _self.$http.post(apiUrl + 'deleteDevelopSubscribeOrder', {developsubscribeorder_id: row.developsubscribeorder_id}).then((response) => {
                                    $('#developTable').bootstrapTable("refresh");
                                }, (response) => {
                                    return common.dealPromptCommon('删除失败');
                                });
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
            function initDevelopTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('subscribeorder_code', '申购单编号', nameFormatter,tableEvents),
                    common.BTRowFormat('develop_code', '项目编号'),
                    common.BTRowFormatAlignLeft('develop_name', '项目名称'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormatAlignLeft('creatorName', '申购人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatAlignLeft('examineName', '审批人'),
                    common.BTRowFormat('subscribeorder_examine_time', '审批时间'),
                    common.BTRowFormatWithFormatterAlignLeft('subscribeorder_refuse_remark', '驳回说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self,'subscribeorder_state', '状态','developSubscribeOrderState'),
                    common.BTRowFormatWithFE('', '操作',actFormatter,tableEvents),
                ];
                $developTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getDevelopSubscribeOrder',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'developsubscribeorder_id',
                    uniqueId: 'developsubscribeorder_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',

                });
                common.changeTableClass($developTable)
            }
            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                initDevelopTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: async function(event) {
                    $('#developTable').bootstrapTable("refresh");
            },
        }
    }
</script>
<style scoped>
</style>
