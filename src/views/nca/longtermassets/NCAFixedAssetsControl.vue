<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">固定资产列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">固定资产列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group" v-show="tableType==1">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_FixedAD" placeholder="搜索固定资产编号、固定资产名称" style="width: 230px;">
                                </div>
                                <div class="form-group" >
                                    <button id="searchFAD" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_AssetP" placeholder="搜索申请单编号、申请部门" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchAP" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==3">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_apply" placeholder="搜索申请单编号、申请部门" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchApply" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group" v-show="tableType==4">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_purchase" placeholder="搜索维修单编号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                             <div class="form-group" v-show="tableType==5">
                                 <div class="form-group">
                                    <input class="form-control" id="search_text_apply_order" placeholder="搜索资产申购编号" style="width: 230px;">
                                 </div>
                                 <div class="form-group">
                                     <button id="searchApplyOrder" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                     </button>
                                 </div>
                            </div>
                            <div class="form-group pull-right" v-show="tableType==2">
                                <button id="addAPO" class="btn btn-info" v-on:click="addAssetPurchaseOrder" >增加
                                </button>                            </div>
                            <div class="form-group pull-right" v-show="tableType==3">
                                <button class="btn btn-info" v-on:click="addApprover" >增加</button>
                            </div>
                            <div class="form-group pull-right" v-show="tableType==5">
                                <button class="btn btn-info" v-on:click="addFixedAssetsOrderAR">生成固定资产验收单</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)">
                                <a href="#fixedAssetsData" data-toggle="tab">
                                    <span>固定资产列表</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)">
                                <a href="#assetPurchase" data-toggle="tab">
                                    <span>资产申购</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(5)">
                                  <a href="#assetsAcceptanceOrder" data-toggle="tab">
                                     <span>资产验收</span>
                                  </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#assetsAcceptance" data-toggle="tab">
                                    <span>资产验收单列表</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#assetMaintenance" data-toggle="tab">
                                    <span>资产维修</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="fixedAssetsData">
                                <table id="fixedAssetsDataTable"></table>
                            </div>
                            <div class="tab-pane fade" id="assetPurchase">
                                <table id="assetPurchaseTable"></table>
                            </div>
                            <div class="tab-pane fade" id="assetsAcceptance">
                                <table id="assetsAcceptanceTable"></table>
                            </div>
                            <div class="tab-pane fade" id="assetMaintenance">
                                <table id="assetMaintenanceTable"></table>
                            </div>
                            <div class="tab-pane fade" id="assetsAcceptanceOrder">
                                <table id="assetsAcceptanceOrderTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AdddDocumentMDModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">查看固定资产列表</h4>
                    </div>
                    <form @submit.prevent="submitForm" id="formDMDC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>固定资产名称</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.fixedassets_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.fixedassets_model" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.fixedassets_unit" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">预计使用年限</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.use_time_limit" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>固定资产分类</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="fixedassets_category" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">预计净残值率</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.residual_value_rate" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>已计提折旧月数</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.deprecition_month" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>已计提折旧金额</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.deprecition_price" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>剩余折旧月数</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.residual_deprecition_month" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>折旧方法</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="depreciation_category" data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                            </div>
                            <!--<div class="row">-->
                                <!--<div class="form-group col-sm-12" style="padding-left: 6px">-->
                                    <!--<label class="col-md-2 control-label"><span class="table-required">*</span>归属部门名称</label>-->
                                    <!--<div class="col-md-9">-->
                                        <!--<input class="form-control" id="department_name" data-parsley-required="true" disabled>-->
                                    <!--</div>-->
                                    <!--<div class="col-sm-1 padding-top-7">-->
                                        <!--<label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 6px">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门</label>
                                    <div class="col-md-9" >
                                        <input class="form-control" id="department_id2" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 6px">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9" style="">
                                        <input class="form-control" id="user_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label"  style="padding-right: 3px">附件</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input id="file" type="file" style="margin-top: 10px" >
                                        <div style="margin-top: 10px">
                                            <p  v-for="(file,index) in detail.files" style="font-size:16px" >
                                                <a :href=file.file_url target="_blank">
                                                    <i class="glyphicon glyphicon-file"  style="font-size:22px"></i>
                                                    {{file.file_name}}
                                                </a>
                                                <i class="glyphicon glyphicon-remove-sign delete_icon" v-on:click="removeImage(index,file)" ></i>
                                            </p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">修改</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddmeetingRoomModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增资产申购单列表</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formMR">
                        <div class="modal-body">
                            <div class="form-horizontal">
                                <div class="form-group">
                                    <label class="col-md-2 control-label">申够部门</label>
                                    <div class="col-md-8">
                                        <input class="form-control" name='department_id' id="department_id"  data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(3)">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveRMAct">提交</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddMaintenanceModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增</h4>
                    </div>
                    <form @submit.prevent="submitM" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-9">
                                    <label class="col-sm-5 control-label"><span class="table-required">*</span>要求完成时间</label>
                                    <div class="col-sm-7">
                                        <input type="text" class="form-control" id="repair_plan_time" data-parsley-required="true" autocomplete="off">
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

        <div class="modal fade" id="AdddDocumentOrderMDModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">修改固定资产列表</h4>
                    </div>
                    <form @submit.prevent="submitOrderForm" id="formOrderDMDC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>固定资产名称</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="detailData.fixedassets_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="detailData.fixedassets_model" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="detailData.fixedassets_unit" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>固定资产分类</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="fixedassets_category_order" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>折旧方法</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="depreciation_category_order" data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>验收类型</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="fixedassetscheck_acceptance_order"  data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row" >
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" ><span class="table-required">*</span>归属部门</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" id="department_id_order" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(4)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label" ><span class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="user_id_order" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(5)">选择</label>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">修改</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <userNewDepartment :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewDepartment>
        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

import userNewDepartment from '../baseconfig/userNewDepartment.vue';
import userSelectDialog from '../../../components/common/userSelectDialog.vue'
export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            pagePara3:{},
            workRow:{},
            tableType:1,
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            index: '',
            mulityFlag: '',
            user_id: '',
            department_id2: '',
            detail:{
                files:[]
            },
            fixedassetscheck_id2: '',
            fixedassets_no2: '',
            fixedassetscheckdetail_id2: '',
            checkDetailFixed: [],
            department_id_order: '',
            user_id_order: '',
            detailData: {}
        }
    },
    name: 'NCAFixedAssetsControl',
    components: {
       userNewDepartment,
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $fixedAssetsDataTable = $('#fixedAssetsDataTable');
        let $assetPurchaseTable = $('#assetPurchaseTable');
        let $assetsAcceptanceTable = $('#assetsAcceptanceTable');
        let $assetMaintenanceTable = $('#assetMaintenanceTable');
        let $assetsAcceptanceOrderTable = $('#assetsAcceptanceOrderTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            common.initSelect2($('#fixedassets_category'), _self.pagePara.fixedassetstype);
            common.initSelect2($('#depreciation_category'), _self.pagePara.depreciationmethodtype);
            common.initSelect2($('#purch_state'), _self.pagePara.purchstate);
            common.initSelect2($('#scrap_flag'), _self.pagePara.scraptype);
            common.initSelect2($('#fixedassets_category_order'), _self.pagePara.fixedassetstype);
            common.initSelect2($('#depreciation_category_order'), _self.pagePara.depreciationmethodtype);
            common.initSelect2($('#fixedassetscheck_acceptance_order'), _self.pagePara.acceptancetype);
            common.initDateTimespicker($('#repair_plan_time'));

            initFixedAssetsDataTable();
            initAssetPurchaseTable();
            initAssetsAcceptanceTable();
            initAssetMaintenanceTable();
            initAssetsAcceptanceOrderTable();
            common.reSizeCall();
        }
        //固定资产数据管理
        function queryParamsAll(params) {
            params.search_text=$('#search_text_FixedAD').val();
            return JSON.stringify(params)
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail btn-info-table">查看</a>',
                '<a class="btn btn-info btn-xs m-r-5 repair_detail btn-info-table">报修</a>'
            ].join('')
        }
        function operateOrderFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_order_detail btn-info-table">修改</a>'
            ].join('')
        }
        function fixedOrderFormatter(value, row, index) {
            if (row.check_state == 0 || row.check_state == 2) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 delete_order btn-info-table">删除</a>'
                ].join('')
            }
        }

        function initFixedAssetsDataTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)

                    $('#fixedassets_category').val(_self.workRow.fixedassets_category).trigger('change');
                    $('#depreciation_category').val(_self.workRow.depreciation_category).trigger('change');
                    $('#scrap_flag').val(_self.workRow.scrap_flag).trigger('change');
                    // $('#department_name').val(_self.workRow.department_name).trigger('change');
                    $('#department_id2').val(_self.workRow.department_name).trigger('change');
                    $('#user_id').val(_self.workRow.user_name).trigger('change');
                    _self.user_id = row.user_id;
                    _self.department_id2 = row.department_id;
                    _self.detail=row;
                    $('#formDMDC').parsley().reset();
                    $('#AdddDocumentMDModal').modal('show')
                },
                'click .repair_detail': function(e, value, row, index) {
                    $('#repair_plan_time').val('');
                    $('#AddMaintenanceModal').modal('show');
                    _self.fixedassetscheck_id2 = row.fixedassetscheck_id;
                    _self.fixedassets_no2 = row.fixedassets_no;
                    _self.fixedassetscheckdetail_id2 = row.fixedassetscheckdetail_id
                    console.log('',_self.fixedassetscheck_id2)
                    console.log('',_self.fixedassets_no2)
                    console.log('',_self.fixedassetscheckdetail_id2)
                    // common.dealConfrimCommon('确认报修？', async () => {
                    //     _self.$http.post(apiUrl + 'add_r', params).then(response => {
                    //         $('#assetMaintenanceTable').bootstrapTable("refresh");
                    //         common.dealSuccessCommon('报修成功');
                            // $("#hhh").addClass("active");
                        // }, (response) => {
                        //     common.dealErrorCommon(this, response);
                        // });
                    // });
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('fixedassets_no', '固定资产编号'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('fixedassets_unit', '计量单位'),
                common.BTRowFormatEdSelect2Disabled(_self, 'fixedassets_category', '固定资产分类', 'fixedassetstype'),
               /* common.BTRowFormat('use_time_limit', '预计使用年限'),
                common.BTRowFormat('residual_value_rate', '预计净残值率'),
                common.BTRowFormatEdSelect2Disabled(_self, 'depreciation_category', '折旧方法', 'depreciationmethodtype'),
                common.BTRowFormat('deprecition_month', '已计提折旧月数'),
                common.BTRowFormat('deprecition_price', '已计提折旧金额'),
                common.BTRowFormat('residual_deprecition_month', '剩余折旧旧月数'),*/
                // common.BTRowFormat('department_id', '归属部门编号'),
                // common.BTRowFormatAlignLeft('department_name', '归属部门名称'),
                common.BTRowFormat('created_at', '购入验收时间'),
                // common.BTRowFormatWithFE('files', '附件', common.filesFormatter),
                // common.BTRowFormatAlignLeft('user_name', '管理责任人'),
                common.BTRowFormatEdSelect2Disabled(_self, 'scrap_flag', '报废标志', 'scraptype'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $fixedAssetsDataTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_fa',
                queryParams: queryParamsAll,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
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
                onEditableSave: async (field, row, oldValue, $el) => {
                    common.rowModifyWithT(_self, apiUrl + 'modify_fa', row, 'fixedassetscheckdetail_id', $('#fixedAssetsDataTable'))
                }
            });
            common.changeTableClass($fixedAssetsDataTable)
        }
        //资产申购
        function nameFormatter2(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsNet(params) {
            params.search_text=$('#search_text_AssetP').val();
            return JSON.stringify(params)
        }
        function initAssetPurchaseTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetPurchaseDetailControl',
                        query: {
                            fixedassetspurch_id: row.fixedassetspurch_id
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('fixedassetspurch_no', '申请单编号', nameFormatter2, tableEvents),
                common.BTRowFormatAlignLeft('department_name', '申请部门'),
                common.BTRowFormat('created_at', '申请时间'),
                common.BTRowFormatEdSelect2Disabled(_self, 'purch_state', '状态', 'purchstate'),
                common.BTRowFormatAlignLeft('purch_checker_name', '审批人'),
                common.BTRowFormat('purch_check_date', '审批时间'),
                common.BTRowFormatAlignLeft('purch_refuse_remark', '驳回说明')
            ];
            $assetPurchaseTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_p',
                queryParams: queryParamsNet,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetspurch_id',
                uniqueId: 'fixedassetspurch_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($assetPurchaseTable)
        }
        //资产验收列表
        function purchaseApplyState(value, row) {
            for (let i = 0; i < _self.pagePara.purchstate.length; i++) {
                if (_self.pagePara.purchstate[i].id == value) {
                    return _self.pagePara.purchstate[i].text
                }
            }
            return ''
        }
        function nameFormatter(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }
        function queryParamsApple(params) {
            params.search_text=$('#search_text_apply').val();
            return JSON.stringify(params)
        }
        function initAssetsAcceptanceTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetsAcceptanceDetailControl',
                        query: {
                            fixedassetscheck_id: row.fixedassetscheck_id,
                            fixedassetscheck_no: row.fixedassetscheck_no
                        }
                    })
                },
                "click .delete_order" :function (e, value, row, index){
                    common.rowDeleteWithApi(_self, '确认删除吗？', apiUrl + 'delete_fd', $assetsAcceptanceTable, row, 'fixedassetscheck_id')
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('fixedassetscheck_no', '资产验收单编号', nameFormatter, tableEvents),
                common.BTRowFormatAlignLeft('user_name', '验收人'),
                common.BTRowFormat('created_at', '验收时间'),
                common.BTRowFormatWithFormatter('check_state', '状态', purchaseApplyState),
                common.BTRowFormatAlignLeft('check_checker_name', '审批人'),
                common.BTRowFormatAlignLeft('check_refuse_remark', '驳回说明'),
                common.BTRowFormatWithFE('', '操作', fixedOrderFormatter, tableEvents)
            ];
            $assetsAcceptanceTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_c',
                queryParams: queryParamsApple,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($assetsAcceptanceTable)
        }
        //资产维修
        function MaintenanceState(value, row) {
            for (let i = 0; i < _self.pagePara.repairstate.length; i++) {
                if (_self.pagePara.repairstate[i].id == value) {
                    return _self.pagePara.repairstate[i].text
                }
            }
            return ''
        }

        function queryParamsRepair(params) {
            params.search_text=$('#search_text_purchase').val();
            return JSON.stringify(params)
        }

        function initAssetMaintenanceTable() {
            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAFixedAssetsRepairMaterials',
                        query: {
                            fixedassetsrepair_id: row.fixedassetsrepair_id,
                            fixedassets_no: row.fixedassets_no,
                            fixedassets_name: row.fixedassets_name,
                        }
                    })
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('fixedassetsrepair_no', '固定资产维修单编号', nameFormatter, tableEvents),
                common.BTRowFormat('fixedassets_no', '固定资产编号'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('repair_plan_time', '要求完成时间'),
                common.BTRowFormatWithFormatter('repair_state', '状态', MaintenanceState),
                common.BTRowFormatAlignLeft('repair_checker_name', '执行人')
    //            common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $assetMaintenanceTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_r',
                queryParams: queryParamsRepair,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetsrepair_id',
                uniqueId: 'fixedassetsrepair_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass($assetsAcceptanceTable)
        }

        //资产验收
        function queryParamsOrder(params) {
            params.search_text = $('#search_text_apply_order').val();
            params.scrap_type = 1;
            return JSON.stringify(params);
        }

        function initAssetsAcceptanceOrderTable  () {
            window.tableEvents = {
                'click .fixedSelect': function(e, value, row, index) {
                    if ($(this).prop('checked')) {
                        _self.checkDetailFixed.push(row);
                    } else {
                        _self.checkDetailFixed.splice($.inArray(row, _self.checkDetailFixed), 1);
                    }
                },
                'click .edit_order_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.detailData = $.extend(true, {}, row)

                    $('#fixedassets_category_order').val(_self.detailData.fixedassets_category).trigger('change');
                    $('#depreciation_category_order').val(_self.detailData.depreciation_category).trigger('change');
                    $('#fixedassetscheck_acceptance_order').val(_self.detailData.fixedassetscheck_acceptance).trigger('change');
                    $('#department_id_order').val(_self.detailData.department_name).trigger('change');
                    $('#user_id_order').val(_self.detailData.user_name).trigger('change');
                    _self.user_id_order = row.user_id;
                    _self.department_id_order = row.department_id;
                    $('#formOrderDMDC').parsley().reset();
                    $('#AdddDocumentOrderMDModal').modal('show')
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.actFormatter('checkAct', () => {return `<input type="checkbox" class="fixedSelect">`}, tableEvents),
                common.BTRowFormat('fixedassetspurch_no', '资产申购编号'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('fixedassets_unit', '计量单位'),
                common.BTRowFormatWithFE('', '操作', operateOrderFormatter, tableEvents)
            ];
            $assetsAcceptanceOrderTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_pdc',
                queryParams: queryParamsOrder,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                sortOrder:'asc',
                idField: 'fixedassetscheck_id',
                uniqueId: 'fixedassetscheck_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                }
            });
            common.changeTableClass($assetsAcceptanceOrderTable)
        }

        $(function() {
            initData();

            common.fileUpload(_self, $('#file'), apiUrl, function(fileInfo) {
                let convert = {} //转换字段名
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                _self.detail.files.push(convert);
                $('#file').val('');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        })
    },
    methods: {
        showTable:function(index){
            let _self = this;
            _self.tableType=index;
            if(index === 1 ){
                $('#fixedAssetsDataTable').bootstrapTable("refresh")
            } else if (index === 2 ){
                $('#assetPurchaseTable').bootstrapTable("refresh")
            } else if (index === 3){
                $('#assetsAcceptanceTable').bootstrapTable("refresh")
            } else if (index === 4){
                $('#assetMaintenanceTable').bootstrapTable("refresh")
            } else if (index === 5){
                _self.checkDetailFixed = [];
                $('#assetsAcceptanceOrderTable').bootstrapTable("refresh")
            }
        },
        queryConfirm: function(event) {
            let _self = this;
            if(_self.tableType === 1 ){
                $('#fixedAssetsDataTable').bootstrapTable("refresh")
            } else if (_self.tableType === 2 ){
                $('#assetPurchaseTable').bootstrapTable("refresh")
            } else if (_self.tableType === 3){
                $('#assetsAcceptanceTable').bootstrapTable("refresh")
            } else if (_self.tableType === 4){
                $('#assetMaintenanceTable').bootstrapTable("refresh")
            } else if (_self.tableType ===  5){
                $('#assetsAcceptanceOrderTable').bootstrapTable("refresh")
            }
        },
        submitForm: async function(event) {
            if ($('#formDMDC').parsley().isValid()) {
                let _self = this,params=$.extend(true,{},_self.detail);
                _self.workRow.files=[];
                for(let i of _self.detail.files){
                    if(!i.file_id){
                        _self.workRow.files.push(i);
                    }
                }
                try {
                    _self.workRow.fixedassetscheckdetail_id = _self.fixedassetscheckdetail_id;
                    _self.workRow.fixedassets_category = common.getSelect2Val('fixedassets_category');
                    _self.workRow.depreciation_category = common.getSelect2Val('depreciation_category');
                    _self.workRow.fixedassetscheck_acceptance = common.getSelect2Val('fixedassetscheck_acceptance');
                    _self.workRow.department_id = _self.department_id2;
                    _self.workRow.user_id = _self.user_id;

                    common.rowModifyWithT(_self, apiUrl + 'modify_fa', _self.workRow, 'docdetail_id', $('#fixedAssetsDataTable'))
                    console.log('success');
                    $('#AdddDocumentMDModal').modal('hide');
                    $('#formDMDC').parsley().reset();
                    window.setTimeout(function () {
                        $('#fixedAssetsDataTable').bootstrapTable("refresh")
                    },500)
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        addAssetPurchaseOrder: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#meetinguser_id').val(null).trigger('change');
            $('#AddmeetingRoomModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formMR').parsley().isValid()) {
                try {
                    _self.workRow.department_id = _self.pagePara3.userDepartmentId;

                    let response = await _self.$http.post(apiUrl + 'add_p', _self.workRow);
                    let fixedassetspurch_id = response.body.info.fixedassetspurch_id
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetPurchaseDetailControl',
                        query: {
                            fixedassetspurch_id: fixedassetspurch_id
                        }
                    })
                    $('#assetPurchaseTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formMR').parsley().reset();
                    $('#AddmeetingRoomModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(index) {
            let _self = this;
            _self.index = index;
            if (_self.index == 1) {
                _self.eventHub.$emit('show-departmentselect-dialog');
            } else if (_self.index == 2) {
                _self.eventHub.$emit('show-userselect-dialog');
            } else if (_self.index == 4) {
                _self.eventHub.$emit('show-departmentselect-dialog');
            } else if (_self.index == 5) {
                _self.eventHub.$emit('show-userselect-dialog');
            } else {
                _self.eventHub.$emit('show-departmentselect-dialog');
            }
        },
        departmentSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeDepartment', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                    if (_self.index == 1){
                        $("#department_id2").val(_self.pagePara3.userDepartmentName)
                        _self.department_id2 = _self.pagePara3.userDepartmentId
                    } else if (_self.index == 4){
                        $("#department_id_order").val(_self.pagePara3.userDepartmentName)
                        _self.department_id_order = _self.pagePara3.userDepartmentId;
                    } else {
                        $("#department_id").val(_self.pagePara3.userDepartmentName)
                        _self.department_id = _self.pagePara3.userDepartmentId
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        userSelectCallback: async function(users) {
            let _self = this;
            let params =  {
                user_id: _self.pagePara.userInfo.user_id,
                users: users
            }
            try {
                _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    if (_self.index == 2){
                        $("#user_id").val(_self.pagePara2.meetingName)
                        _self.user_id = _self.pagePara2.meetingId
                    } else if (_self.index == 5) {
                        $("#user_id_order").val(_self.pagePara2.meetingName)
                        _self.user_id_order = _self.pagePara2.meetingId;
                    }

                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        addApprover:async function () {
            let _self = this;
            let params={apply_approver:''};
            common.dealConfrimCommon('确认新增固定资产验收单？', async () => {
                _self.$http.post(apiUrl + 'add_c', params).then(response => {
                    let fixedassetscheck_id = response.body.info.fixedassetscheck_id;
                    let fixedassetscheck_no = response.body.info.fixedassetscheck_no;
                    _self.$router.push({
                        path: '/nca/longtermassets/NCAAssetsAcceptanceDetailControl',
                        query: {
                            fixedassetscheck_id: fixedassetscheck_id,
                            fixedassetscheck_no: fixedassetscheck_no
                        }
                    })
                    $('#assetsAcceptanceTable').bootstrapTable("refresh");
                }, (response) => {
                    common.dealErrorCommon(this, response);
                });
            });
        },
        removeImage:function (index,file) {
            let _self=this;
            _self.detail.files.splice(index, 1);
            if(file.file_id){
                var params={
                    file_id:file.file_id
                }
                _self.$http.post(apiUrl + 'removeFile', params).then(response =>{
                    if(response.body.errno==0){
                    }else {
                        console.log(response.msg)
                    }
                },(response) =>{
                    common.dealErrorCommon(response);
                })
            }
        },
        submitM: async function(event) {
            let _self = this;

            if ($('#formA').parsley().isValid()) {
                try {
                    let params={fixedassetscheck_id: _self.fixedassetscheck_id2,
                                fixedassets_no: _self.fixedassets_no2,
                                fixedassetscheckdetail_id: _self.fixedassetscheckdetail_id2,
                                repair_plan_time: $('#repair_plan_time').val()
                    };
                    _self.$http.post(apiUrl + 'add_r', params).then(response => {
                        $('#assetMaintenanceTable').bootstrapTable("refresh");
                        common.dealSuccessCommon('报修成功');
                        $('#formA').parsley().reset();
                        $('#AddMaintenanceModal').modal('hide');
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        addFixedAssetsOrderAR:async function () {
            let _self = this;
            let tableData=_self.checkDetailFixed;
            let fixedassetspurchdetailId ='';
            for(let i in tableData){
                fixedassetspurchdetailId=fixedassetspurchdetailId+tableData[i].fixedassetspurchdetail_id+',';
            }

            fixedassetspurchdetailId=(fixedassetspurchdetailId.slice(fixedassetspurchdetailId.length-1)==',')?fixedassetspurchdetailId.slice(0,-1):fixedassetspurchdetailId;
            if(tableData.length===0){
                return  common.dealWarningCommon('请选择固定资产！');
            }
            for(let i=0;i<tableData.length;i++){
                if (tableData[i].fixedassetscheck_acceptance == null || tableData[i].user_id == null ||
                    tableData[i].department_id == null || tableData[i].depreciation_category == null ||
                    tableData[i].fixedassets_category == null) {
                    return  common.dealWarningCommon('请完善固定资产！');
                    _self.checkDetailFixed = [];
                }
            }
            let params={
                checkDetailFixed:_self.checkDetailFixed,
                purchDetailIds:fixedassetspurchdetailId,
                scrap_type:1
            }
            console.log('params',params)
            this.$http.post(apiUrl + 'submit_pdc', params).then((response) => {
                let retData = response.data.info;
                window.setTimeout(function () {
                    $('#assetsAcceptanceOrderTable').bootstrapTable("refresh");
                },500);
                common.dealSuccessCommon('保存成功');
                _self.checkDetailFixed = [];
                fixedassetspurchdetailId = ''
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        submitOrderForm: async function(event) {
            if ($('#formOrderDMDC').parsley().isValid()) {
                let _self = this;
                try {
                    _self.detailData.fixedassets_category = common.getSelect2Val('fixedassets_category_order');
                    _self.detailData.depreciation_category = common.getSelect2Val('depreciation_category_order');
                    _self.detailData.fixedassetscheck_acceptance = common.getSelect2Val('fixedassetscheck_acceptance_order');
                    _self.detailData.department_id = _self.department_id_order;
                    _self.detailData.user_id = _self.user_id_order;

                    common.rowModifyWithT(_self, apiUrl + 'modify_pd', _self.detailData, 'docdetail_id', $('#assetsAcceptanceOrderTable'));
                    console.log('success');
                    $('#assetsAcceptanceOrderTable').bootstrapTable("refresh");
                    _self.detailData = {};
                    _self.checkDetailFixed = [];
                    $('#AdddDocumentOrderMDModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
}
</script>
<style scoped>
</style>
