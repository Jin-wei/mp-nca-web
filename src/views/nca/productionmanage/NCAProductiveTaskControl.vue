<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产计划管理</a></li>
            <li class="active">生产任务单列表</li>
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
                                    <input class="form-control" id="search_text" placeholder="搜索物料编码、名称" style="width: 230px;">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body" style="padding: 15px">
                        <div  id="set_table">
                            <table id="setTable"></table>
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
            }
        },
        name: 'NCAProductiveTaskControl',
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');

            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
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
                        path: '/nca/productionmanage/NCAProductiveTaskDetailControl',
                        query: {
                            productivetask_id: row.productivetask_id
                        }
                    })
                }
            };
            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('productivetask_code', '生产任务单号', nameFormatter,tableEvents),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormat('materiel_unit', '计量单位'),
                    common.BTRowFormatAlignRight('taskdesign_number', '数量'),
                    common.BTRowFormat('order_id', '订单号')
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'productivetask_id',
                    uniqueId: 'productivetask_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'

                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                console.log('_self.pagePara',_self.pagePara)
                initsetTable();
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            }
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
