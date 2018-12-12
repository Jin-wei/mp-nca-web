<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产管理</a></li>
            <li class="active">生产主计划列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">生产主计划列表</h4>
                    </div>
                    <div class="form-inline panel-toolbar" role="form">
                        <div class="form-line">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="请输入生产任务单号" id="orderproductplan_id" style="width: 180px">
                                <input type="text" class="form-control" placeholder="请输入销售订单" id="order_id" style="width: 180px">
                                <input type="text" class="form-control" id="process_begin_date" placeholder="开始时间"/>
                                <span>-</span>
                                <input type="text" class="form-control" id="process_end_date" placeholder="结束时间"/>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                                <!--<div class="form-group">-->
                                    <!--<input type="text" class="form-control" id="mrpDate" placeholder="mrp运算日期" />-->
                                <!--</div>-->
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="testPlan">生成计划</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;margin-top: 0px">
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/productionmanage/NCAMasterPlanControl?method='
    export default {
        data: function() {
            return {
                workRow: {},
                oldRow: '',
                pagePara: {}
            }
        },
        name: 'NCAMasterPlanControl',
//        components: {
//            DomainChooseHeader
//        },
        mounted: function() {
            let _self = this
            let $table = $('#table')

            function queryParams(params) {
                let domain_id = common.getSelect2Val('domain_select')
                if (domain_id) {
                    params.domain_id = domain_id
                }
                params.orderproductplan_id = $('#orderproductplan_id').val();
                params.order_id = $('#order_id').val();
                params.process_begin_date = $('#process_begin_date').val();
                params.process_end_date = $('#process_end_date').val();
                return JSON.stringify(params)
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getMainPaln',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('orderproductplan_id', '生产任务单号'),
                        common.BTRowFormatAlignLeft('process_name', '工序名称'),
                        common.BTRowFormat('construction_site', '施工场地'),
                        common.BTRowFormat('process_begin_date', '开始日期'),
                        common.BTRowFormatEditable('process_end_date', '结束日期'),
                        common.BTRowFormat('process_state', '施工状态'),
                        common.BTRowFormat('order_id', '销售订单'),
                        common.BTRowFormat('materiel_name', '产品'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'plan_state', '任务状态', 'planState'),
                        common.BTRowFormatWithFormatterAlignLeft('description', '描述',common.remarkFormatter),
                        common.BTRowFormatAlignLeft('name', '指派人'),

                    ],
                    idField: 'orderproductplan_id',
                    uniqueId: 'orderproductplan_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    },
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        let params={}
                        params.orderproductplan_id = row.orderproductplan_id
                        params.process_end_date = row.process_end_date
                        params.process_begin_date = row.process_begin_date
                        _self.$http.post(apiUrl + 'modifyProcessDate', params).then((response) => {
                            $('#table').bootstrapTable("refresh")
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })

                    }
                })
                common.changeTableClass($table)
            }

            async function initPage() {
                await _self.getPagePara()
                common.initSelect2($('#plan_state'), _self.pagePara.planState);
                common.initDatepicker($('#process_begin_date'));
                common.initDatepicker($('#process_end_date'));
                common.initDatepicker($('#mrpDate'));
                initTable();
                common.reSizeCall();

            }

            initPage()
        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            queryConfirm: function(event) {
                $('#table').bootstrapTable("refresh")
            },
            testPlan:async function(event){
                let _self = this;
                let params = {mrp_date:$("#mrpDate").val()};
                _self.$http.post(apiUrl + 'dataExtract', params).then((response) => {
                    common.dealSuccessCommon('成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
        }
    }
</script>
<style scoped>
    .m_t{
        margin-top:15px;
        /*margin-left:10px;*/
    }
    .m_r{
        right: 0px;
    }
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        right:-350px;
        top:0px;
    }
</style>
