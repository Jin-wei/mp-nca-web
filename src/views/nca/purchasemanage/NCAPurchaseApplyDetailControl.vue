<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">采购管理</a></li>
            <li class="active">采购申请单号</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 col-sm-12">
                <div class="widget widget-stats bg-black-lighter">
                    <div class="stats-info">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">采购申请单号</span>
                                    <div>
                                        <input class="form-control" v-model="apply.purchaseapply_id" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">创建时间</span>
                                    <div>
                                        <input class="form-control" v-model="apply.created_at" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon">状态</span>
                                    <div>
                                        <select class="form-control select2" id="apply_state" disabled></select>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon"  >采购申请人</span>
                                    <div>
                                        <input class="form-control" v-model="apply.apply_applicant" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon" >审批人</span>
                                    <div>
                                        <input class="form-control" v-model="apply.apply_approver" readonly>
                                    </div>
                                </div>
                            </div>

                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                </div>
                <h4 class="panel-title">采购申请单</h4>
            </div>
            <div class="panel-body">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control"  id="search_text" placeholder="搜索物料编码,物料名称,销售订单号" style="width: 230px;">
                        </div>
                        <div class="form-group">
                            <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                        <span v-if = !purchaseapplyId>
                        <div class="form-group pull-right">
                            <button id="printApply" class="btn btn-info" v-on:click="printPuchaseApply" >
                                <i class="glyphicon glyphicon-print"></i> 打印
                            </button>
                            <button id="addMateriel" class="btn btn-info" v-on:click="addM">增加物料
                            </button>
                            <button id="saveApply" class="btn btn-info" v-on:click="saveOrderMateriel" >提交
                            </button>
                        </div>
                        </span>
                    </div>
                    <div class="col-md-12 no-padding" style="margin-top: 10px">
                        <table id="applyDetailTable"></table>
                    </div>
            </div>
        </div>


        <div class="modal fade" id="AddMaterielModal">
            <div class="modal-dialog" style="width: 1100px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增物料记录</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3" style="padding-left: 0px">
                            <input class="form-control"  id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="getMateriel">
                        </div>
                        <!--<div class="form-group col-xs-3" style="width:200px">-->
                            <!--<select class="form-control select2" id="xm_order"></select>-->
                        <!--</div>-->
                        <div class="form-group col-xs-3" style="width:200px">
                            <select class="form-control select2" id="order_id"></select>
                        </div>
                        <div class="form-group col-xs-3" style="width:200px">
                            <select class="form-control select2" id="room_id"></select>
                        </div>
                        <div class="form-group col-xs-3" style="width:200px">
                            <button type="button" class="btn btn-info" v-on:click="addPuchaseApplyDetail">保存</button>
                        </div>
                        <table id="tableSearch"></table>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    import Constant from '../../../assets/js/constant'
    const common = require('commonFunc');
    const apiUrl = '/api/nca/purchasemanage/NCAPurchaseApplyDetailControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{},
                purchaseapply_id:'',
                apply:{},
                searchedRow:[],
                purchaseapplyId:'',
                order_type:'',
                show_return: 0
            }
        },
        name: 'NCAPurchaseApplyDetailControl',
        mounted: function() {
            let _self = this;
            let $applyDetailTable = $('#applyDetailTable');
            let $tableSearch = $('#tableSearch');
            _self.purchaseapplyId = _self.$route.query.purchaseapplyId
            _self.order_type = _self.$route.query.order_type
            async function initData() {
                let response = await _self.$http.post(apiUrl + 'initAct', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                common.initSelect2($('#apply_state'), _self.pagePara.purchaseApplyType);
                // common.initSelect2($('#xm_order'), _self.pagePara.projectInfo);
                //common.initSelect2Placeholder($('#order_id'), _self.pagePara.order,'请选择销售单号');
                //common.initSelect2Placeholder($('#room_id'), [],'请选择空间');
                _self.purchaseapply_id = common.getUrlParams('purchaseapply_id');
                if (_self.purchaseapply_id == null) {
                    _self.purchaseapply_id = _self.$route.query.purchaseapplyId
                }
                response = await _self.$http.post(apiUrl + 'getPuchaseApply', {purchaseapply_id:_self.purchaseapply_id});
                _self.apply = JSON.parse(JSON.stringify(response.data.info[0]));
                $('#apply_state').val(_self.apply.apply_state).trigger('change');

                let xmOrder = await _self.$http.post(apiUrl + 'getPOA', {xm_order:1});
                let orderId = xmOrder.data.info;
                $("#order_id").html('');
                common.initSelect2Editable($('#order_id'), orderId);

                initAllDemandTable();
                initTableMateriel();
                common.reSizeCall();
            }
            function initTableNum(value, row) {
                return 0
            }
            function queryParams(params) {
                params.purchaseapply_id=_self.purchaseapply_id;
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initAllDemandTable() {
                window.tableEvents = {
                    'click .delete_purchaseApplyDetail': function(e, value, row, index) {
                        if(_self.apply.apply_state==1){
                            return common.dealPromptCommon('该申请单已提交')
                        }else if (_self.apply.apply_state==2){
                            return common.dealPromptCommon('该申请单已通过')
                        }
                        common.rowDeleteWithApi(_self, '采购申请单详情删除', apiUrl + 'deletePurchaseApplyDetail', $applyDetailTable, row, 'purchaseapplydetail_id', function() {})
                    }
                };

                let columsArr = [];
//                if (_self.order_type==1) {
//                    columsArr = [
//                        common.BTRowFormat('materiel_code', '物料编码'),
//                        common.BTRowFormat('materiel_name', '物料名称'),
//                        common.BTRowFormat('materiel_format', '规格型号'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
//                        common.BTRowFormat('apply_number', '数量'),
//                        common.BTRowFormat('order_id', '销售单号'),
//                        common.BTRowFormat('apply_money', '金额'),
//                        common.actFormatter('act', () => {
//                            return '<a class="btn btn-primary btn-xs m-r-5 process delete_purchaseApplyDetail">删除</a>';
//                        }, tableEvents)
//                    ];
//                } else {
                    columsArr = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('apply_number', '数量'),
                        common.BTRowFormatWithFormatterAlignRight('apply_money', '金额',common.priceFormat),
                        common.BTRowFormat('order_id', '销售单号'),
                        common.BTRowFormat('project_space_id', '施工项编号'),
                        common.actFormatter('act', () => {
                            return '<a class="btn btn-info btn-xs m-r-5 process delete_purchaseApplyDetail">删除</a>';
                        }, tableEvents)
                    ];
//                }
                $applyDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getPuchaseApplyDetail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'purchaseapplydetail_id',
                    uniqueId: 'purchaseapplydetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($applyDetailTable)
            }
            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
                return JSON.stringify(params)
            }
            function initTableMateriel() {
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getSupplerMateiel',
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
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEditableRight('apply_number', '默认数量'),
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
                    },
                    onUncheckAll: function(rows) {
                        _self.searchedRow = []
                    },
                    onCheck: function(row, $element) {
                        _self.searchedRow.push(row)
                    },
                    onUncheck: function(row, $element) {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    }
                });
                common.changeTableClass($tableSearch)
            }

            // $('#xm_order').on('select2:select', async function(evt) {
            //     if(common.getSelect2Val('xm_order') == 1){
            //         let params={
            //             xm_order:common.getSelect2Val('xm_order')
            //         }
            //         let response = await _self.$http.post(apiUrl + 'getPOA', params);
            //         let retData = response.data.info;
            //         $("#order_id").html('');
            //         common.initSelect2Editable($('#order_id'), retData);
            //     }else{
            //         let params={
            //             xm_order:common.getSelect2Val('xm_order')
            //         }
            //         let response = await _self.$http.post(apiUrl + 'getPOB', params);
            //         let retData = response.data.info;
            //         $("#order_id").html('');
            //         common.initSelect2Editable($('#order_id'), retData);
            //     }
            //
            // });

            $('#order_id').on('select2:select', async function(evt) {
                // if(common.getSelect2Val('xm_order') == 1){
                    let params={
                        order_id:common.getSelect2Val('order_id')
                    };
                    let response = await _self.$http.post(apiUrl + 'getOrderpace', params);
                    $("#room_id").empty();
                    common.initSelect2Placeholder($('#room_id'), response.data.info,'请选择空间');
                    $('#room_id').val(null).trigger('change');
                // }
            });


            $(function() {
                initData();
            })
        },
        methods: {

            queryConfirm: function(event) {
                $('#applyDetailTable').bootstrapTable("refresh")
            },
            addM: function(event) {
                let _self = this;
                if(_self.apply.apply_state==1){
                    return common.dealPromptCommon('该申请单已提交')
                }else if (_self.apply.apply_state==2){
                    return common.dealPromptCommon('该申请单已通过')
                }
                $('#AddMaterielModal').modal('show');
                _self.searchedRow = [];
                $('#room_id').val(null).trigger('change');
                $('#order_id').val(null).trigger('change');
                $("#matNameOrCodeOrFormat").val('');
                $('#tableSearch').bootstrapTable("refresh");
                $("input[type=checkbox]").each(function() {
                    $(this).prop("checked", false);
                })
            },
            getMateriel:function(event){
                let _self = this;
                $('#tableSearch').bootstrapTable("refresh")
            },
            addPuchaseApplyDetail:function(event){
                let _self = this;
                let params=[];
                // let xm_order=common.getSelect2Val('xm_order');
                // console.log('_self.order_type',_self.order_type)
                if(!_self.order_type){
                    _self.order_type=1;
                }

                // if(xm_order == 1){
                    let order_id=common.getSelect2Val('order_id');
                    if(order_id==''){
                        return common.dealPromptCommon('请选择销售单号')
                    }
                    let room_id=common.getSelect2Val('room_id');
                    if(room_id==''){
                        return common.dealPromptCommon('请选择空间')
                    }
                    if(_self.order_type==2){
                        return common.dealPromptCommon('该申请单已存在项目施工编号，不能选择销售单号')
                    }

                    if(_self.searchedRow.length==0){
                        return common.dealPromptCommon('请选择物料')
                    }
                    for(let s of _self.searchedRow){
                        if(s.apply_number && s.apply_number!=0){
                            params.push({
                                purchaseapply_id:_self.purchaseapply_id,
                                order_id:order_id,
                                materiel_id:s.materiel_id,
                                apply_number:s.apply_number,
                                room_id:room_id,
                                xm_order: 1
                            })
                        } else {
                            return common.dealPromptCommon('请填写默认数量')
                        }
                    }
                    _self.$http.post(apiUrl + 'addPurchaseApplyDetail', params).then((response) => {
                        let retData = response.data.info;
                        $('#applyDetailTable').bootstrapTable("refresh");
                        _self.searchedRow=[];
                        $('#AddMaterielModal').modal('hide');
                        common.dealSuccessCommon('保存成功 ');
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                // }else{
                //     if(_self.order_type==1){
                //         return common.dealPromptCommon('该申请单已存在销售单号，不能选择项目施工编号')
                //     }
                //     let order_id=common.getSelect2Val('order_id');
                //     if(order_id==''){
                //         return common.dealPromptCommon('请选择施工项编号')
                //     }
                //     for(let s of _self.searchedRow){
                //         if(s.apply_number && s.apply_number!=0){
                //             params.push({
                //                 purchaseapply_id:_self.purchaseapply_id,
                //                 order_id:order_id,
                //                 materiel_id:s.materiel_id,
                //                 apply_number:s.apply_number,
                //                 room_id:'',
                //                 xm_order: 2
                //             })
                //         } else {
                //             return common.dealPromptCommon('请填写默认数量')
                //         }
                //     }
                //     _self.$http.post(apiUrl + 'addPurchaseApplyDetail', params).then((response) => {
                //         let retData = response.data.info;
                //         $('#applyDetailTable').bootstrapTable("refresh");
                //         _self.searchedRow=[];
                //         $('#AddMaterielModal').modal('hide');
                //         common.dealSuccessCommon('保存成功');
                //     }, (response) => {
                //         common.dealErrorCommon(_self, response)
                //     });
                // }


                $('#AddModal').modal('hide')
            },
            saveOrderMateriel:function(event){
                let _self = this;
                if(_self.apply.apply_state==1){
                    return common.dealPromptCommon('该申请单已提交')
                }else if (_self.apply.apply_state==2){
                    return common.dealPromptCommon('该申请单已通过')
                }
                let tableData=$('#applyDetailTable').bootstrapTable('getData');
                if(tableData.length===0){
                    return  common.dealWarningCommon('请先增加数据！');
                }
                let params={
                    purchaseapply_id:_self.purchaseapply_id
                }
                _self.$http.post(apiUrl + 'setTask', params).then((response) => {
                    let retData = response.data.info;
                    $('#tableSearch').bootstrapTable("refresh");
                    _self.searchedRow=[];
                    common.dealSuccessCommon('提交成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            },
            printPuchaseApply: function () {
                let _self = this;
                Constant.setData('materielInfo', this.pagePara);
                _self.$router.push({
                    path: '/nca/purchasemanage/NCAPuchaseApplyPirntControl',
                    query: {
                        purchaseapply_id: _self.purchaseapply_id
                    }
                });
            }
        }
    }
</script>
<style scoped>
</style>
