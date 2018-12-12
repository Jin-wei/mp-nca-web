<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">生产管理</a></li>
            <li class="active">生产物料计划列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <!--<DomainChooseHeader :pagePara="pagePara" :apiName="apiName"></DomainChooseHeader>-->
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">生产物料计划列表</h4>
                    </div>
                    <div class="form-inline panel-toolbar" role="form">
                        <div class="form-line p_r">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="请输入生产任务单号" id="orderproductplan_id" style="width: 180px">
                                <input type="text" class="form-control" placeholder="请输入销售订单" id="order_id" style="width: 180px">
                                <span>请输入查询天数</span>
                                <input type="text" class="form-control" placeholder="请输入查询天数" value="4" id="dateNum" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addDm">发货指令
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;margin-top: 0px">
                        <table id="table"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">发货指令</h4>
                    </div>
                    <form @submit.prevent="addDm" id="formA">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-5 control-label">指派给:</label>
                                <div class="col-sm-5">
                                    <select class="form-control" id="plan_assign" v-model="workRow.plan_assign"></select>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info"><i class="fa fa-fw fa-plus"></i>提交</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/productionmanage/NCAWeeklyPlanControl?method='
//    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    export default {
        data: function() {
            return {
                taskArr: [],
                workRow: {},
                oldRow: '',
                pagePara: {},
                searchedRow: [],
//                apiName: common.getApiName(apiUrl)
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
                params.dateNum = $('#dateNum').val();
                return JSON.stringify(params)
            }

            function actCheckBox(value, row) {
                return [
                    '<input type="radio" name="searchRow" class="searchRow">'
                ].join('')
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getWeeklyPaln',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        {
                            field: 'state',
                            checkbox: true
                        },
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
                        common.BTRowFormatAlignLeft('name', '审核人'),

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
                    onCheckAll: function(rows) {
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchedRow.push(rows[i])
                        }
                    },
                    onUncheckAll: function(rows) {
                        _self.searchedRow = []
                    },
                    onCheck: function(row, $element) {
                        _self.searchedRow.push(row)
                    },
                    onUncheck: function(row, $element) {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    },
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        let params={}
                        for (var i = 0; i < _self.searchedRow.length; i++) {
                            params.orderproductplan_id = _self.searchedRow[i].orderproductplan_id
                            params.process_end_date = _self.searchedRow[i].process_end_date
                            params.process_begin_date = _self.searchedRow[i].process_begin_date
                            _self.$http.post(apiUrl + 'modifyProcessDate', params).then((response) => {
                                $('#table').bootstrapTable("refresh")
                            }, (response) => {
                                common.dealErrorCommon(_self, response)
                            })
                        }
                    }
                })
                common.changeTableClass($table)
            }

            async function initPage() {
                await _self.getPagePara()
                common.initSelect2($('#plan_assign'), _self.pagePara.user)
                common.initSelect2($('#plan_state'), _self.pagePara.planState)
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
            addM: function(event) {
                let _self = this
                _self.workRow = {}
                _self.taskArr = []
                $('#plan_assign').val(null).trigger('change');
                $('#AddModal').modal('show')
            },
            addDm: function() {
                let _self = this;
                let params={};

                if(_self.searchedRow.length==0){
                    return common.dealWarningCommon('请选择生产计划')
                }
                params.taskArr=[];
                for (var i = 0; i < _self.searchedRow.length; i++) {
                    if(_self.searchedRow[0].plan_state==1 || _self.searchedRow[0].plan_state==2){
                        return common.dealWarningCommon('请选择未指派或者驳回的生产计划')
                    }
                    params.taskArr.push({
                        materiel_name:_self.searchedRow[i].materiel_name,
                        process_name:_self.searchedRow[i].process_name,
                        order_id:_self.searchedRow[i].order_id,
                        orderproductplan_id:_self.searchedRow[i].orderproductplan_id
                    })
                }
                common.dealConfrimCommon('确认指派任务？', async () => {
                    _self.$http.post(apiUrl + 'setTask', params).then(response => {
                        $('#table').bootstrapTable("refresh");
                        _self.searchedRow=[];
                        common.dealSuccessCommon('指派成功');
                    }, (response) => {
                        common.dealErrorCommon(_self, response);
                    });
                });
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
