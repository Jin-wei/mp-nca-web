<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">任务列表</a></li>
        <li class="active">资产盘点单详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">申请单号</span>
                                <div>
                                    <input class="form-control" v-model="take_stock_no" readonly>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn" style="position:relative">
                        <a style="position: absolute;right:3px;" href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">盘点单详情</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_AID" placeholder="搜索盘点对象工号、姓名" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                                <button class="btn btn-info" v-show="take_stock_detail_status ==0 || take_stock_detail_status ==1" v-on:click="addFixedAssets" >提交盘点
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <table id="assetIDTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCATakeStockSRV?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            take_stock_no:'',
            apply:{},
            take_stock_detail_status: ''
        }
    },
    name: 'NCAAssetInventoryTaskDetailControl',
    mounted: function() {
        let _self = this;
        let $assetIDTable = $('#assetIDTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            _self.take_stock_no = common.getUrlParams('take_stock_no');

            response = await _self.$http.post(apiUrl + 'search_lt', {take_stock_parent_no:_self.take_stock_no});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.take_stock_detail_status = _self.apply.take_stock_detail_status;
            $('#take_stock_detail_status').val(_self.apply.take_stock_detail_status).trigger('change');
            initAllDemandTable();
            common.reSizeCall();
            $('#formAID').parsley()
        }

        function queryParams(params) {
            params.search_text=$('#search_text_AID').val();
            params.take_stock_parent_no=common.getUrlParams('take_stock_no');
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-primary btn-xs m-r-5 show_detail btn-info-table">查看</a>'
            ].join('')
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetInventoryUsersTaskDetailControl',
                        query: row
                    })
                }
            };

            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('user_id', '盘点对象工号'),
                common.BTRowFormatAlignLeft('name', '盘点对象姓名'),
                common.BTRowFormatAlignLeft('position_name', '岗位'),
                common.BTRowFormatAlignLeft('department_name', '部门'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $assetIDTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchDetail',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'take_stock_detail_id',
                uniqueId: 'take_stock_detail_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_pd', row, 'take_stock_detail_id', $assetIDTable)
                }
            });
            common.changeTableClass($assetIDTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetIDTable').bootstrapTable("refresh")
        },
        addFixedAssets: function(event) {
            let _self = this;
            if (_self.take_stock_detail_status==2){
                return common.dealPromptCommon('该申请单已通过')
            }
            let params={
                take_stock_no: common.getUrlParams('take_stock_no'),
                task_id: common.getUrlParams('taskId')
            }
            _self.$http.post(apiUrl + 'confirm', params).then((response) => {
                let retData = response.data.info;
                $('#assetIDTable').bootstrapTable("refresh");
                common.dealSuccessCommon('提交成功');
                window.history.back()
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        }
    }
}
</script>
<style scoped>
</style>
