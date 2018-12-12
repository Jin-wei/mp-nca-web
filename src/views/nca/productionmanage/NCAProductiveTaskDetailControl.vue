<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产任务管理</a></li>
            <li class="active">生产任务单详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">采购列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_all" placeholder="搜索物料编码,物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group" >
                                    <button id="searchAll" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#procedure" data-toggle="tab">
                                    <span>车间及工序</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#feeding" data-toggle="tab">
                                    <span>投料命令</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#related1" data-toggle="tab">
                                    <span>联产品</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#related2" data-toggle="tab">
                                    <span>边余料</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="procedure">
                                <table id="procedureTable"></table>
                            </div>
                            <div class="tab-pane fade" id="feeding" >
                                <table id="feedingTable"></table>
                            </div>
                            <div class="tab-pane fade" id="related1">
                                <table id="related1Table"></table>
                            </div>
                            <div class="tab-pane fade" id="related2">
                                <table id="related2Table"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/productionmanage/NCAProductiveTaskControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                productivetask_id:''
            }
        },
        name: 'NCAProductiveTaskDetailControl',
        mounted: function() {
            let _self = this;

            _self.productivetask_id = _self.$route.query.productivetask_id;
            let $procedureTable = $('#procedureTable');
            let $feedingTable = $('#feedingTable');
            let $related1Table = $('#related1Table');
            let $related2Table = $('#related2Table');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                initProcedureTable();
                initFeedingTable();
                initRelated1Table();
                initRelated2Table();
                common.reSizeCall();
            }
            //工序
            function queryParamsAll(params) {
                params.search_text=$('#search_text_all').val();
                params.productivetask_id = _self.productivetask_id
                return JSON.stringify(params)
            }
            function initProcedureTable() {
                let columsArr = [
//                    common.BTRowFormat('materiel_code', '生产车间'),
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEdSelect2(_self, 'workshop_id', '生产车间', 'departmentInfo'),
                    common.BTRowFormat('priority', '工序优先级'),
                    common.BTRowFormat('procedure_name', '生产工序'),
                ];
                $procedureTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getProcedure',
                    queryParams: queryParamsAll,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'product_procedure_id',
                    uniqueId: 'product_procedure_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($procedureTable)
            }
            //投料
            function queryParamsNet(params) {
                params.search_text=$('#search_text_net').val();
                params.productivetask_id = _self.productivetask_id
                return JSON.stringify(params)
            }
            function initFeedingTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormat('materiel_unit', '计量单位'),
                    common.BTRowFormatAlignRight('taskdetaildesign_number', '投料数量'),
                    common.BTRowFormat('taskdetailprd_level', '层级'),
                ];
                $feedingTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getFeeding',
                    queryParams: queryParamsNet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'productivetaskdetail_id',
                    uniqueId: 'productivetaskdetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($feedingTable)
            }
            //联产品

            function queryParamsApple(params) {
                params.search_text=$('#search_text_apply').val();
                params.taskrelated_type = 1
                params.productivetask_id = _self.productivetask_id
                return JSON.stringify(params)
            }
            function initRelated1Table() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormat('materiel_unit', '计量单位'),
                    common.BTRowFormatAlignRight('taskrelateddesign_number', '联产数量')
                ];
                $related1Table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getRelated',
                    queryParams: queryParamsApple,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'productivetaskrelated_id',
                    uniqueId: 'productivetaskrelated_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($related1Table)
            }
            //边余料
            function queryParamsPurchase(params) {
                params.search_text=$('#search_text_purchase').val();
                params.taskrelated_type = 2
                params.productivetask_id = _self.productivetask_id
                return JSON.stringify(params)
            }
            function initRelated2Table() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormat('materiel_unit', '计量单位'),
                    common.BTRowFormatAlignRight('taskrelateddesign_number', '数量')
                ];
                $related2Table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getRelated',
                    queryParams: queryParamsPurchase,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'productivetaskrelated_id',
                    uniqueId: 'productivetaskrelated_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($related2Table)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            showTable:function(index){
                let _self = this;
                _self.tableType=index;
                if(index === 1 ){
                    $('#procedureTable').bootstrapTable("refresh")
                } else if (index === 2 ){
                    $('#feedingTable').bootstrapTable("refresh")
                } else if (index === 3){
                    $('#related1Table').bootstrapTable("refresh")
                }else if (index === 4){
                    $('#related2Table').bootstrapTable("refresh")
                }
            },
            queryConfirm: function(event) {
                let _self = this;
                if(_self.tableType === 1 ){
                    $('#procedureTable').bootstrapTable("refresh")
                } else if (_self.tableType === 2 ){
                    $('#feedingTable').bootstrapTable("refresh")
                } else if (_self.tableType === 3){
                    $('#related1Table').bootstrapTable("refresh")
                } else if (_self.tableType === 4){
                    $('#related2Table').bootstrapTable("refresh")
                }
            }
        }
    }
</script>
<style scoped>
</style>
