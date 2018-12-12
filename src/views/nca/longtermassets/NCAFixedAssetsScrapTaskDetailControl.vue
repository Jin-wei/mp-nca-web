<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">资产报废管理</a></li>
            <li class="active">资产报废管理申请单详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">长期资产报废</h4>
                    </div>
                    <div class="panel-body">
                        <div id="toolbar" class="pull-left">
                            <div class="form-inline" role="form">
                                <div class="form-group" style="margin-bottom: 10px">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_textFAS" placeholder="搜索资产编号、固定名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group ">
                                        <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                            <i class="fa fa-search"></i>
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <table id="FixedAssetsScrspDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAAssetRetirementControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            longassetsscrap_id:'',
            longassetsscrap_no:'',
            apply:{},
            searchedRow:[],
            selectState: '',//1.固定资产 2.待摊资产 3.低值易耗品
            show_return: 0
        }
    },
    name: 'NCAFixedAssetsScrapTaskDetailControl',
    mounted: function() {
        let _self = this;
        let $FixedAssetsScrspDetailTable = $('#FixedAssetsScrspDetailTable');

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#scrap_state'), _self.pagePara.purchstate);
            _self.show_return = _self.$route.query.show_return;
            console.log('show_return',_self.show_return)

            _self.longassetsscrap_id = common.getUrlParams('longassetsscrap_id');
            _self.longassetsscrap_no = common.getUrlParams('longassetsscrap_no');

            response = await _self.$http.post(apiUrl + 'search_adt', {longassetsscrap_id:_self.longassetsscrap_id});
            _self.apply = JSON.parse(JSON.stringify(response.data.info));
            _self.selectState = _self.apply.scrap_type;
            $('#scrap_state').val(_self.apply.scrap_state).trigger('change');

            initAllDemandTable();
            common.reSizeCall();
        }
        function queryParams(params) {
            params.longassetsscrap_id=_self.longassetsscrap_id;
            params.search_text=$('#search_textFAS').val();
            params.scrap_type=_self.selectState;
            return JSON.stringify(params)
        }
        function initAllDemandTable() {
            let columsArr = []
            if(_self.selectState==1){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormatAlignRight('return_price', '可回收金额'),
                    common.BTRowFormatAlignRight('expend_price', '消耗费用')
                ];
            }else if(_self.selectState==2){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('amortize_code', '项目编号'),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    common.BTRowFormatAlignRight('return_price', '可回收金额'),
                    common.BTRowFormatAlignRight('expend_price', '消耗费用')
                ];
            }else if(_self.selectState==3){
                columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormat('consumables_unit', '计量单位'),
                    common.BTRowFormatAlignRight('return_price', '可回收金额'),
                    common.BTRowFormatAlignRight('expend_price', '消耗费用')
                ];
            }
            $FixedAssetsScrspDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'longassetsscrapdetail_id',
                uniqueId: 'longassetsscrapdetail_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_d', row, 'longassetsscrapdetail_id', $FixedAssetsScrspDetailTable)
                }
            });
            common.changeTableClass($FixedAssetsScrspDetailTable)
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            $('#FixedAssetsScrspDetailTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>