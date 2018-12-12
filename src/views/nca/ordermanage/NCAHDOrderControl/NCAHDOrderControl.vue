<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单管理</a></li>
            <li class="active">总部订单列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">总部订单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_fit" placeholder="搜索订单号、姓名、手机号、地址、机构名称" style="width: 280px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="order_state_fit">
                                        <option value="">请选择订单状态</option>
                                        <option v-for="state in pagePara.orderStateFitInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                            </div>


                            <div class="form-group" v-show="tableType==7">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_group" placeholder="搜索订单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="order_state_group">
                                        <option value="">请选择订单状态</option>
                                        <option v-for="state in pagePara.orderStateGroupInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="land_agent_group">
                                        <option value="">请选择地产商</option>
                                        <option v-for="state in pagePara.landAgentList" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                            </div>


                            <div class="form-group" v-show="tableType==8">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_sales" placeholder="搜索订单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="order_state_sales">
                                        <option value="">请选择订单状态</option>
                                        <option v-for="state in pagePara.orderStateSalesInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                            </div>


                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                                </div>
                            </div>


                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>

                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#fit_order" data-toggle="tab">
                                    <span>装修订单</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(7)" style="display: none">
                                <a href="#group_order" data-toggle="tab">
                                    <span>团体订单</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(8)" style="display: none">
                                <a href="#sales_order" data-toggle="tab">
                                    <span>销售订单</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fit_order">
                                <table id="fitOrderTable"></table>
                            </div>
                            <div class="tab-pane fade" id="group_order" >
                                <table id="groupOrderTable"></table>
                            </div>
                            <div class="tab-pane fade" id="sales_order">
                                <table id="salesOrderTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="QRModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">二维码</h4>
                    </div>
                    <div class="modal-body" v-html="QRHtml">

                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/ordermanage/NCAHDOrderControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                QRHtml:'',
                workRow:{}
            }
        },
        name: 'NCAHDOrderControl',
        components: {
        },
        mounted: function() {
            let _self = this;
            let $fitOrderTable = $('#fitOrderTable');
            let $groupOrderTable = $('#groupOrderTable');
            let $salesOrderTable = $('#salesOrderTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }
            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 tableDelete btn-info-delete" style="padding-right: 0;padding-left: 0;margin-right: 0!important">查看二维码</a>',
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    if(_self.tableType==1){
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAHDOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }else if (_self.tableType==7){
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAHDOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }else if (_self.tableType=='8'){
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAHDOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }
                },
                'click .tableDelete': function(e, value, row, index) {

                    _self.$http.post(apiUrl + 'qrcode', {order_id: row.order_id}).then((response) => {
                        $('#QRModal').modal('show');
                        _self.QRHtml = response.data.info.qrcode;
                        console.log(_self.QRHtml)
                    }, (response) => {
                    });
                }
            };
            //装修订单
            function queryParamsFit(params) {
                params.search_text=$('#search_text_fit').val();
                params.order_state =$('#order_state_fit').val();
                params.createdBTime= $('#start_date').val();
                params.createdETime= $('#start_end').val();

                return JSON.stringify(params)
            }
            function initFitOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号', nameFormatter,tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatWithFormatterAlignLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormatAlignRight('order_house_area', '面积（平米）'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'project_type', '工程类型', 'projectTypeInfo'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '订单状态', 'orderStateFitInfo'),
                   /* common.BTRowFormat('designer_name', '设计师'),*/
                    common.BTRowFormat('create_date', '创建日期'),
                    common.BTRowFormat('recommender_phone', '推荐人手机'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'domain_id', '门店', 'domainInfo'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ];
                $fitOrderTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_fit',
                    queryParams: queryParamsFit,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'order_id',
                    uniqueId: 'order_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'order_id', $fitOrderTable)
                    },
                    onPostBody: function () {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        });
                    }
                });
                common.changeTableClass($fitOrderTable)
            }
            //团体订单
            function queryParamsGroup(params) {
                params.search_text=$('#search_text_group').val();
                params.order_state =$('#order_state_group').val();
                params.land_agent = $('#land_agent_group').val();
                params.createdBTime= $('#start_date').val();
                params.createdETime= $('#start_end').val();
                return JSON.stringify(params)
            }
            function initGroupOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatWithFormatterAlignLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormatAlignRight('order_house_area', '面积（平米）'),
                    common.BTRowFormat('room_type_name', '户型'),
                    common.BTRowFormatAlignLeft('landagent_name', '地产商'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '状态', 'orderStateGroupInfo'),
                    common.BTRowFormat('create_date', '创建日期'),
                    common.BTRowFormatWithFE('', '操作',operateFormatter, tableEvents)
                ];
                $groupOrderTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_group',
                    queryParams: queryParamsGroup,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'order_id',
                    uniqueId: 'order_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($groupOrderTable)
            }
            //销售订单
            function queryParamsSales(params) {
                params.search_text=$('#search_text_sales').val();
                params.order_state =$('#order_state_sales').val();
                params.createdBTime= $('#start_date').val();
                params.createdETime= $('#start_end').val();
                return JSON.stringify(params)
            }
            function initSalesOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('domain_name', '采购方'),
                    common.BTRowFormat('domain_phone', '联系电话'),
                    common.BTRowFormatAlignLeft('domain_contact', '联系人'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '状态', 'orderStateSalesInfo'),
                    common.BTRowFormat('create_date', '创建日期')
                ];
                $salesOrderTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_sales',
                    queryParams: queryParamsSales,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'order_id',
                    uniqueId: 'order_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($salesOrderTable)
            }

            async function initData() {
                common.initDatepicker($('#start_date'));
                common.initDatepicker($('#end_date'));

                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                common.initSelect2($('#project_type'), retData.projectTypeInfo);
                initFitOrderTable();
//                initGroupOrderTable();
//                initSalesOrderTable();
                common.reSizeCall();
            }

            $(async function() {
               await initData();
            })
        },
        methods: {
            showTable:function(index){
                let _self = this;
                _self.tableType=index;

                $('#start_date').val('');
                $('#end_date').val('');

                if(index === 1 ){
                    $('#fitOrderTable').bootstrapTable("refresh")
                } else if (index === 7 ){
                    $('#groupOrderTable').bootstrapTable("refresh")
                } else if (index === 8){
                    $('#salesOrderTable').bootstrapTable("refresh")
                }
            },
            queryConfirm: function(event) {
                let _self = this;
                if(_self.tableType === 1 ){
                    $('#fitOrderTable').bootstrapTable("refresh")
                } else if (_self.tableType === 7 ){
                    $('#groupOrderTable').bootstrapTable("refresh")
                } else if (_self.tableType === 8){
                    $('#salesOrderTable').bootstrapTable("refresh")
                }
            }
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
