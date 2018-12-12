<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">固定资产申购管理</a></li>
        <li class="active">申购单详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">申购单号</span>
                                <div>
                                    <input class="form-control" v-model="apply.fixedassetspurch_no" readonly>
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
                    <div class="panel-heading-btn">
                        <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">申购单详情</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_APTDC" placeholder="搜索固定资产名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <table id="assetPurchaseTaskDetailTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            fixedassetspurch_id:'',
            apply:{},
            purch_state: '',
            show_return: 0
        }
    },
    name: 'NCADocumentManagementDetailControl',
    mounted: function() {
        let _self = this;
        let $assetPurchaseTaskDetailTable = $('#assetPurchaseTaskDetailTable');
        let $fileTable = $('#documentFilesTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#purch_state'), _self.pagePara.purchstate);
            _self.show_return = _self.$route.query.show_return;
            console.log('show_return',_self.show_return)

            _self.fixedassetspurch_id = common.getUrlParams('fixedassetspurch_id');
            response = await _self.$http.post(apiUrl + 'search_pdt', {fixedassetspurch_id:_self.fixedassetspurch_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            let retData = response.data.info;
            _self.purch_state = _self.apply.purch_state;
            $('#purch_state').val(_self.apply.purch_state).trigger('change');

            initAllDemandTable();
            common.reSizeCall();
        }

        function queryParams(params) {
            params.search_text=$('#search_text_APTDC').val();
            params.fixedassetspurch_id=common.getUrlParams('fixedassetspurch_id');
            return JSON.stringify(params)
        }

        function initAllDemandTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('fixedassets_unit', '计量单位'),
                common.BTRowFormatAlignRight('fixedassets_num', '数量')
            ];

            $assetPurchaseTaskDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_pd',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetspurchdetail_id',
                uniqueId: 'fixedassetspurchdetail_id',
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

                }
            });
            common.changeTableClass($assetPurchaseTaskDetailTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetPurchaseTaskDetailTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
