<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单管理</a></li>
            <li class="active">个人订单列表</li>
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
                        <h4 class="panel-title">个人订单列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_sale" placeholder="搜索订单号" style="width: 210px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="order_state_sale">
                                        <option value="">请选择订单状态</option>
                                        <option v-for="state in pagePara.orderStateSalesInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="user_ids">
                                        <option value="">请选择客户姓名</option>
                                        <option v-for="id in pagePara.userInfo" v-bind:value="id.user_id">{{id.name}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                                </div>
                                <div class="form-group">
                                    <span>-</span>
                                    <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                                </div>
                                <div class="form-group ">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addMPurchase" class="btn btn-info" v-on:click="addMPurchase">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="salesOrderTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 v-if="workRow.order_id" class="modal-title">编辑订单</h4>
                        <h4 v-else class="modal-title">新建订单</h4>
                    </div>
                    <form @submit.prevent="saveAct" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户手机</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.phone" @blur="searchbyphone" data-parsley-phone="true" data-parsley-required="true" data-parsley-group="block-1">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.name"  data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" data-parsley-group="block-1">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">工程类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="project_type"  data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px">推荐人手机</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="recommender_phone" v-model="workRow.recommender_phone" data-parsley-group="block-1">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control select2" id="gantt_remark" v-model="workRow.order_remark"></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveAct">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModalPurchase" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 v-if="workRow.order_id" class="modal-title">编辑订单</h4>
                        <h4 v-else class="modal-title">新建订单</h4>
                    </div>
                    <form @submit.prevent="saveSaleOrder" id="formC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="cliten_type"  data-parsley-required="true" ></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>选择客户</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="client_search"  data-parsley-required="true" ></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <!--<h4 class="modal-title">新增物料</h4>-->
                        <h4 v-if="workRow.order_id" class="modal-title">新增物料</h4>
                        <h4 v-else class="modal-title">编辑物料</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <input class="form-control"  id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="getMateriel">
                        </div>
                        <div class="form-group col-xs-3" >
                            <button type="button" class="btn btn-info" v-on:click="addOrderItem">保存</button>
                        </div>
                        <table id="tableSearch"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/ordermanage/NCASaleOrderControl?method=';
    const materielApiUrl = '/api/nca/purchasemanage/NCAPurchaseApplyDetailControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:8,
                QRHtml:'',
                workRow:{},
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                searchedRow:[],
                saleOrderId:'',
                purchasrOrderId:'',
                oldRow:''
            }
        },
        name: 'NCASaleOrderControl',
        components: {
            DomainChooseHeader
        },
        mounted: function() {
            let _self = this;
            let $salesOrderTable = $('#salesOrderTable');
            let $tableSearch = $('#tableSearch');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                   if (_self.tableType=='8'){
                        _self.$router.push({
                            path: '/nca/ordermanage/NCASOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }
                },
                'click .edit_detail': function(e, value, row, index) {
                    _self.saleOrderId = row.order_id;
                    _self.purchasrOrderId = row.purchase_order_id;
                    $('#tableSearch').bootstrapTable("refresh")
                    $('#AddMaterielModal').modal('show');
                },
                'click .delete_detail': function(e, value, row, index) {
                    _self.saleOrderId = row.order_id;
                    _self.purchasrOrderId = row.purchase_order_id;
                    _self.$http.post(apiUrl + 'deleteSaleOrder', {SOID:_self.saleOrderId,POID:_self.purchasrOrderId}).then((response) => {
                        $('#salesOrderTable').bootstrapTable("refresh")
                    }, (response) => {
                    });
                },
                'click .export_detail': function(e, value, row, index) {

                    _self.$http.post(apiUrl + 'exportSaleData', {order_id:row.order_id}).then((response) => {
                        let downUrl = response.data.info;
                        window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            };

            // 销售订单
            function queryParamsSales(params) {
                params.search_text=$('#search_text_sale').val();
                params.order_state =$('#order_state_sale').val();
                params.createdBTime= $('#start_date').val();
                params.createdETime= $('#start_end').val();
                params.user_id = $('#user_ids').val();
                params.purchaser_type = 2;
                return JSON.stringify(params)
            }
            function actFormatter(value, row) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑物料</a>',
                    '<a class="btn btn-info btn-xs m-r-5 export_detail">导出</a>',
                    '<a class="btn btn-info btn-xs m-r-5 delete_detail">删除</a>'
                ].join('')
            }

            function initSalesOrderTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('order_id', '订单号',nameFormatter, tableEvents),
                    common.BTRowFormatEdSelect2Disabled(_self, 'purchaser_type', '采购方类型', 'clitenType'),
//                    common.BTRowFormat('domain_name', '机构名称'),
//                    common.BTRowFormat('domain_phone', '联系电话'),
//                    common.BTRowFormat('domain_contact', '联系人'),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户联系电话'),
//                    common.BTRowFormatEdSelect2Disabled(_self, 'order_state', '状态', 'orderStateSalesInfo'),
                    common.BTRowFormatEdSelect2(_self, 'sap_order_state', 'SAP状态', 'sapState'),
                    common.BTRowFormat('create_date', '创建日期'),
                    common.actFormatter('act', actFormatter, tableEvents)
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
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'order_id', $salesOrderTable)
                    }
                });
                common.changeTableClass($salesOrderTable)
            }
           // 物料列表
            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                return JSON.stringify(params)
            }
            function initMaterielTable(){
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: materielApiUrl + 'getSupplerMateiel',
                    queryParams: queryParamsMateriel,
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
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatWithFormatterAlignLeft('materiel_name', '物料名称',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('materiel_format', '规格型号',common.remarkFormatter),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEnumberWMinALginRight('apply_number', '默认数量'),
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10,15,25,50,100],
                    locale: 'zh-CN',
                    onCheckAll: function(rows) {
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchedRow.push(rows[i])
                        }
                        console.log(_self.searchedRow)
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
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
            }
            async function initData() {
                common.initDatepicker($('#start_date'));
                common.initDatepicker($('#end_date'));

                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                common.initSelect2($('#project_type'), retData.projectTypeInfo);
                common.initSelect2($('#cliten_type'), retData.customerTypeTwo);
                common.initSelect2($('#client_search'), []);

                initSalesOrderTable();
                initMaterielTable();
                common.reSizeCall();
            }

            $('#cliten_type').on('select2:select', async function(evt) {
                $('#client_search').html('');
                if(common.getSelect2Val('cliten_type')==1){
                    let storeList = _self.pagePara.storeList;

                    for(var i=0;i<storeList.length;i++){
                        if((storeList[i].text).indexOf(_self.userinfo.domain_name)>-1){
                            storeList.splice(i,1);
                        }
                    }

                    common.initSelect2($('#client_search'),storeList);
                }else if (common.getSelect2Val('cliten_type')==2){
                    common.initSelect2($('#client_search'), _self.pagePara.customerInfo);
                }else {
                    common.initSelect2($('#client_search'), _self.pagePara.companyInfo);
                }

            });

         //从客户管理中创建的快速下单
         function quickAdd () {
           let phone = _self.$route.query.phone
           let name = _self.$route.query.name

           if (phone && name){
             _self.workRow = {}
             _self.workRow.phone = phone
             _self.workRow.name = name

             $('#project_type').val(null).trigger('change');
             $('#AddModal').modal('show');
           }
         }

            $(function() {
                initData();
                quickAdd();
//                console.log(common.checkAuth('NCAGLOBALORDERCONTROL', _self.userinfo.menulist))
            })
        },
        methods: {
            showTable:function(index){
                let _self = this;
                _self.tableType=index;

                $('#start_date').val('');
                $('#end_date').val('');

                if (index === 8){
                    $('#salesOrderTable').bootstrapTable("refresh")
                }
            },
            queryConfirm: function(event) {
                let _self = this;
                if (_self.tableType === 8){
                    $('#salesOrderTable').bootstrapTable("refresh")
                }
            },

            addM: function(event) {
                let _self = this;
                _self.workRow = {};
                $('#project_type').val(null).trigger('change');
                $('#AddModal').modal('show');
            },
            searchbyphone: function(event) {
                let _self = this;
                async function getUser() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'searchPhone', _self.workRow);
                        let retData = response.data.info;
                        if (!$.isEmptyObject(retData)) {
                            _self.$set(_self.workRow, 'name', retData.name)
                        }
                    } catch (error) {
                        common.dealErrorCommon(_self, error)
                    }
                }

                if (_self.workRow.phone) {
                    getUser()
                }
            },
            addMPurchase: function(event) {
                let _self = this;
                $('#client_search').html('');
                $('#cliten_type').val(null).trigger('change')
                $('#AddModalPurchase').modal('show');
            },
            saveSaleOrder: async function(event) {
                let _self = this;
                let formC = $('#formC');
                if (formC.parsley().isValid()) {
                    try {
                        if(common.getSelect2Val('cliten_type')==1){
                            let params = {
                                clientType:common.getSelect2Val('cliten_type'),
                                domain_id:common.getSelect2Val('client_search')
                            };
                            let response = await _self.$http.post(apiUrl + 'addSaleOrder', params);
                        }else if (common.getSelect2Val('cliten_type')==2){
                            let params = {
                                clientType:common.getSelect2Val('cliten_type'),
                                purchaser_user_id:common.getSelect2Val('client_search')
                            };
                            let response = await _self.$http.post(apiUrl + 'addSaleOrder', params);
                        }else {
                            let params = {
                                clientType:common.getSelect2Val('cliten_type'),
                                purchaser_corporateclients_id:common.getSelect2Val('client_search')
                            };
                            let response = await _self.$http.post(apiUrl + 'addSaleOrder', params);
                        }

                        _self.queryConfirm();
//                        $('#salesOrderTable').bootstrapTable("refresh");
                        formC.parsley().reset();
                        $('#AddModalPurchase').modal('hide');

                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },

            getMateriel:function(event){
                let _self = this;
                $('#tableSearch').bootstrapTable("refresh")
            },

            addOrderItem:function(event){
                let _self = this;
                let params=[];
                for(let s of _self.searchedRow){
                    if(s.apply_number && s.apply_number!=0){
                        params.push({
                            SOID:_self.saleOrderId,
                            POID:_self.purchasrOrderId,
                            materiel_id:s.materiel_id,
                            apply_number:s.apply_number,
                            materiel_sale:s.materiel_sale
                        })
                    }
                }

                if(params.length==0){
                    return common.dealPromptCommon('请选择物料')
                }

                _self.$http.post(apiUrl + 'addSaleOrderMateriel', params).then((response) => {
                    let retData = response.data.info;
                    _self.searchedRow=[];
                    $('#AddMaterielModal').modal('hide');
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            },
        }
    }
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
