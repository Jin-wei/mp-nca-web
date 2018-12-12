
<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">工程项目管理</a></li>
            <li class="active">工程项目详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading panel-height">
                        <h4 class="panel-title pull-left">工程项目详情</h4>
                        <h4 class="panel-title pull-right ">项目名称：{{projectInfo.project_name}}</h4>
                        <h4 class="panel-title pull-right margin-left">项目编号：{{projectInfo.project_id}}</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <!--budgetGroup -->
                            <div class="form-inline" v-show="tableType==1">
                                <div class="form-group">
                                    <div class="form-group">
                                        <input type="text" class="form-control" placeholder="请输入空间" id="searchText"/>
                                    </div>
                                    <div class="form-group">
                                        <button  class="btn btn-info" v-on:click="showTable(1)">
                                            <i class="fa fa-search"></i>
                                        </button>
                                    </div>
                                </div>
                                <!--<div class="form-group pull-right" style="margin-right: 5px">-->
                                    <!--<button class="btn btn-info" v-on:click="addFinalQuotation">-->
                                        <!--提交-->
                                    <!--</button>-->
                                <!--</div>-->
                            </div>
                            <!--budgetDetail-->
                            <div class="form-inline" v-show="tableType==1.1">
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="select_space_position" @change="showTable(1.1)" style="width: 150px">
                                        <option value="">请选择空间位置</option>
                                        <option v-for="type in pagePara.spacePositionInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="worker_type" @change="showTable(1.1)" style="width: 150px">
                                        <option value="">请选择工种类型</option>
                                        <option v-for="type in pagePara.professionInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <!--<div class="form-group pull-right">-->
                                    <!--<button class="btn btn-info" v-on:click="addClickBudgetDetail" >-->
                                        <!--新增-->
                                    <!--</button>-->
                                <!--</div>-->
                            </div>
                            <!--subscribe-->
                            <div class="form-inline" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_subscribe" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="showTable(2)"><i class="fa fa-search"></i></button>
                                </div>
                                <div class="form-group pull-right">
                                    <!--<div class="form-group">-->
                                        <!--<button id="submitSubscribe" class="btn btn-info" v-on:click="submitSubscribe">提交</button>-->
                                    <!--</div>-->
                                    <!--<div class="form-group" >-->
                                        <!--<button id="addSubscribe" class="btn btn-info" v-on:click="addSubscribe">增加</button>-->
                                    <!--</div>-->
                                    <!--<div class="form-group">-->
                                        <!--<button class="btn btn-info" v-on:click="downloadTemp(2)">下载模板</button>-->
                                    <!--</div>-->
                                    <!--<div class="form-group">-->
                                        <!--<button class="btn btn-info" v-on:click="importData(2)">上传资料</button>-->
                                    <!--</div>-->
                                </div>
                            </div>
                            <!--材料采购-->
                            <div class="form-inline" v-show="tableType==8">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_purchase" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="showTable(8)"><i class="fa fa-search"></i></button>
                                </div>
                                <!--<div class="form-group pull-right">-->
                                    <!--<button id="submitPurchase" class="btn btn-info" v-on:click="submitPurchase">提交</button>-->
                                <!--</div>-->
                                <!--<div class="pull-right">-->
                                <!--<span class="span1">采购单状态: </span>-->
                                <!--</div>-->
                            </div>
                            <!--材料收料-->
                            <div class="form-inline" v-show="tableType==3" >
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_receive" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchReceive" class="btn btn-info" v-on:click="showTable(3)"><i class="fa fa-search"></i></button>
                                </div>
                                <!--<div class="form-group pull-right">-->
                                    <!--<button id="submitReceive" class="btn btn-info" v-on:click="saveReceive">提交</button>-->
                                <!--</div>-->
                            </div>
                            <!--人工结算-->
                            <div class="form-inline" v-show="tableType==4">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_clear" placeholder="搜索施工名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="searchClear" class="btn btn-info" v-on:click="showTable(4)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <!--<div class="form-group">-->
                                        <!--<button id="submitClear" class="btn btn-info" v-on:click="submitClear">提交</button>-->
                                    <!--</div>-->
                                    <div class="pull-right">
                                        <span class="span1">结算状态: {{clearState.text}}</span>
                                        <span class="span1 padding-left">总金额: {{ _self.budgetTotal.total_clearing_reality_money}}</span>
                                    </div>
                                </div>
                            </div>
                            <!--材料耗用-->
                            <div class="form-inline" v-show="tableType==5">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_consume" placeholder="搜索物料名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_consume" class="btn btn-info" v-on:click="showTable(5)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <!--<div class="form-group">-->
                                        <!--<button id="submitConsume" class="btn btn-info" v-on:click="submitConsume">提交</button>-->
                                    <!--</div>-->
                                    <div class="pull-right">
                                        <span class="span1">
                                            耗用单状态：{{consumeState.text}}
                                        </span>
                                    </div>
                                </div>
                            </div>
                            <!--构建费用-->
                            <div class="form-inline" v-show="tableType==6">
                                <div class="form-inline">
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_cost" placeholder="搜索购建费用事项名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_cost" class="btn btn-info" v-on:click="showTable(6)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <!--<div class="form-group ">-->
                                        <!--<button id="submitCost" class="btn btn-info" v-on:click="submitCost">提交</button>-->
                                    <!--</div>-->
                                    <!--<div class="form-group">-->
                                        <!--<button id="addModalBudCost" class="btn btn-info" v-on:click="AddModalCost">增加</button>-->
                                    <!--</div>-->
                                    <div class="form-group pull-right">
                                        <span class="span1">费用单状态：{{costState.text}}</span>
                                        <span class="span1 padding-left">合计：{{_self.costInfo.total_projectcost_money}}</span>
                                    </div>
                                </div>
                            </div>
                            <!--移交验收-->
                            <div class="form-inline" v-show="tableType==7">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_check" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search_check" class="btn btn-info" v-on:click="showTable(7)"><i class="fa fa-search"></i></button>
                                </div>
                                <!--<div class="form-group ">-->
                                    <!--<button id="" class="btn btn-info" v-on:click="submitCheck">提交</button>-->
                                <!--</div>-->
                                <div class="form-group pull-right">
                                    <span class="span1">
                                        验收状态：{{_self.checkState.text}}
                                    </span>
                                </div>
                            </div>

                        </div>
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">

                            <li class="active" @click="showTable(1)" id="budgetTab">
                                <a href="#budget" data-toggle="tab">
                                    <span>购建预算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)" id="subscribeTab">
                                <a href="#subscribe" data-toggle="tab">
                                    <span>材料申购</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(8)">
                                <a href="#purchase" data-toggle="tab">
                                    <span>材料采购</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(3)">
                                <a href="#projectreceive" data-toggle="tab">
                                    <span>材料收料</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#projectclear" data-toggle="tab">
                                    <span>人工结算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(5)">
                                <a href="#projectconsume" data-toggle="tab">
                                    <span>材料耗用</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(6)">
                                <a href="#projectcost" data-toggle="tab">
                                    <span>购建费用</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(7)">
                                <a href="#check_transfer" data-toggle="tab">
                                    <span>验收移交</span>
                                </a>
                            </li>

                        </ul>
                        <div class="tab-content" >

                            <div class="tab-pane fade active in" id="budget">
                                <div v-show="budgetGroupShow">
                                    <table id="budgetGroupTable" ></table>
                                </div>
                                <div v-show="!budgetGroupShow">
                                    <table id="budgetDetailTable"></table>
                                </div>
                            </div>
                            <div class="tab-pane fade" id="subscribe">
                                <table id="subscribeTable"></table>
                            </div>
                            <div class="tab-pane fade" id="purchase">
                                <table id="purchaseTable"></table>
                            </div>
                            <div class="tab-pane fade " id="projectreceive">
                                <table id="projectReceiveTable"></table>
                            </div>
                            <div class="tab-pane fade" id="projectclear">
                                <table id="projectclearTable"></table>
                            </div>
                            <div class="tab-pane fade " id="projectconsume">
                                <table id="projectConsumeTable"></table>
                            </div>
                            <div class="tab-pane fade " id="projectcost">
                                <table id="projectcostTable"></table>
                            </div>
                            <div class="tab-pane fade" id="check_transfer">
                                <div class="panel panel-inverse">
                                    <div class="panel panel-inverse">
                                        <div class="panel-heading panel-height">
                                            <h4 class="panel-title pull-left">决算管理</h4>
                                        </div>
                                    </div>
                                    <div class="panel-body">
                                        <div class="row">
                                            <div class="form-group col-sm-3">
                                                <div class="input-group">
                                                    <span class="input-group-addon">人工决算</span>
                                                    <div>
                                                        <input class="form-control" id="totalClearingLastRealityMoney" v-model="checkInfo.totalClearingLastRealityMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group">
                                                    <span class="input-group-addon">材料决算</span>
                                                    <div>
                                                        <input class="form-control" id="totalConsumeMoney" v-model="checkInfo.totalConsumeMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group ">
                                                    <span class="input-group-addon">购建费用</span>
                                                    <div>
                                                        <input class="form-control" id="totalProjectCostMoney" v-model="checkInfo.totalProjectCostMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                            <div class="form-group col-sm-3">
                                                <div class="input-group ">
                                                    <span class="input-group-addon">费用合计</span>
                                                    <div>
                                                        <input class="form-control" id="totalMoney" v-model="checkInfo.totalMoney" readonly>
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>
                                </div>

                                <div class="panel panel-inverse">
                                    <div class="panel-heading panel-height">
                                        <h4 class="panel-title pull-left">材料结余</h4>
                                    </div>
                                </div>
                                <table id="materialBalanceTable"></table>
                            </div>

                        </div>
                    </div>
                </div>
            </div>
        </div>
        <!--构建预算 add modal-->
        <div class="modal fade" id="addBudgetDetail" data-backdrop="static">
            <div class="modal-dialog" style="width: 700px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增</h4>
                    </div>
                    <form @submit.prevent="submitProjectDetail" id="formProject">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">空间位置</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="space_position" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">施工项</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="space_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">数量</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="space_count" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">材料预算单价</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="material_budget" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">工种</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="space_worker_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info"><i class="fa fa-fw fa-plus"></i>增加</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <!--采购申购 add modal-->
        <div class="modal fade" id="AddModalSubcribe" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增申购项记录</h4>
                    </div>
                    <form @submit.prevent="saveSubscribe" id="formB">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>物料名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="subscribe_name" v-model="workRow.subscribe_name" data-parsley-group="block-1" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.subscribe_format" id="subscribe_format" data-parsley-group="block-1" >
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control select2" v-model="workRow.subscribe_unit" multiple style="width:100%" id="subscribe_unit"  data-parsley-group="block-1" >
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px">数量</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="subscribe_number" v-model="workRow.subscribe_number" data-parsley-group="block-1" data-parsley-type="number" data-parsley-required="true">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10">
                                        <input class="form-control select2" id="subscribe_remark" v-model="workRow.subscribe_remark" >
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="saveAction">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <!--构建费用 add modal-->
        <div class="modal fade" id="AddModalCost" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增购建费用事项记录</h4>
                    </div>
                    <form @submit.prevent="saveCost" id="formD">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-7">
                                    <label class="col-sm-5 control-label"><span class="table-required">*</span>购建费用事项</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" id="projectcost_name" v-model="workRow.projectcost_name" data-parsley-group="block-1" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-5">
                                    <label class="col-sm-4 control-label">有发票金额</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.projectcost_invoice_money" id="projectcost_invoice_money" data-parsley-group="block-1" type="text" data-parsley-required="true" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                               oninput="if(value.length>7)value=value.slice(0,7)">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-7">
                                    <label class="col-sm-5 control-label">无发票金额</label>
                                    <div class="col-sm-7">
                                        <input class="form-control select2" v-model="workRow.projectcost_noinvoice_money" multiple style="width:100%" id="projectcost_noinvoice_money"  data-parsley-group="block-1" type="text" data-parsley-required="true"  onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" class="btn btn-info" id="saveCost">保存</button>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <!--材料采购供应商选择-->
        <div class="modal fade" id="AddModalPurchase" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">供应商选择</h4>
                    </div>
                    <form @submit.prevent="savePurchase" id="formE">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-7">
                                    <label class="col-sm-3 control-label"><span class="table-required">*</span>供应商</label>
                                    <div class="col-sm-9">
                                        <select class="form-control" id="supplier_id" data-parsley-group="block-1" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div>
                                    <button type="submit" class="btn btn-info" id="savePurchase">保存</button>
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <!--最终报价输入框-->
        <div class="modal fade" id="finalQuotation" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">最终报价</h4>
                    </div>
                    <form @submit.prevent="submitClickBudgetGroup" id="formX">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>最终报价</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="project_quoted_amount" v-model="project_quoted_amount" data-parsley-group="block-1" data-parsley-required="true">
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">确认</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCAProjectControl?method=';
    export default {
        data: function() {
            return {
                oldRow:{},
                tableType: '1',
                project_id: '',
                project_detail_id: '',
                project_space_id: '',
                projectInfo: [],
                budgetGroupShow: true,
                pagePara: [],
                searchRow:[],
                workRow:{},
                budgetTotal:{},
                costTotal:{},
                checkInfo:'',
                index: '',
                projectState:'',
                subscribeState:'',
                consumeState:'',
                costState:'',
                clearState:'',
                checkState:'',
                budgetState:'',
                clearingInfo:'',
                clearing_last_finishlimit:'',
                receiverInfo:[],
                r:{},
                costInfo:'',
                budgetInfo:[],
                searchPurchaseRow:[],
                dataType :'',
                userinfo: common.getStoreData('userinfo'),
                fileUrl: null,
                filePath: null,
                project_quoted_amount: 0,
                searchedRow:[],
                projectsubscribeorder_id:''

            }
        },
        name: 'NCAProjectDetailNewControl',
        mounted: function() {
            let _self = this;




            let $budgetGroupTable = $('#budgetGroupTable');
            let $budgetDetailTable = $('#budgetDetailTable');
            let $subscribeTable = $('#subscribeTable');
            let $projectReceiveTable = $('#projectReceiveTable');
            let $projectConsumeTable = $('#projectConsumeTable');
            let $projectcostTable = $('#projectcostTable');
            let $projectclearTable = $('#projectclearTable');
            let $materialBalanceTable =$('#materialBalanceTable');
            let $purchaseTable = $('#purchaseTable');
            function stateFilter(result,source,value) {
                if(result && result.length>0){
                    for (let i = 0; i < source.length; i++) {
                        if (result[0][value] == source[i].id) {
                            return source[i]
                        }
                    }
                }
                return {}
            }
            window.tableEvents = {
                'click .searchBudgetDetail': function(e, value, row, index) {
                    _self.budgetGroupShow = false;
                    _self.project_detail_id = row.project_detail_id;
                    _self.tableType = '1.1';
                    $('#budgetDetailTable').bootstrapTable("refresh")
                },
                'click .deleteGroup': function(e, value, row, index) {
                    let checkState = _self.checkProjectState('budget');
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }

                    let msg =  '确定删除？';
                    common.rowDeleteWithApi(_self, msg, apiUrl + 'delete_detail', $budgetGroupTable, row, 'project_detail_id', function(){
                        $('#budgetGroupTable').bootstrapTable("refresh")
                    })
                },
                'click .subscribeDetail': function (e, value, row, index) {
                    let checkState = _self.checkProjectState('');
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }
                    _self.tableType = '2';
                    _self.project_space_id = row.project_space_id;
                    $("#budgetTab").removeClass("active");
                    $("#subscribeTab").addClass("active");
                    $("#budget").removeClass("active in");
                    $("#subscribe").addClass("active in");
                    $('#subscribeTable').bootstrapTable("refresh")
                },
                'click .deleteSubscribe': function(e, value, row, index) {
                    let checkState = _self.checkProjectState('budget');
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }
                    common.dealConfrimCommon('您确定删除吗？', function () {
                        if(_self.searchRow.length === 0){
                            $('#subscribeTable').bootstrapTable('removeAll');
                        }else{
                            for(let s of _self.searchRow){
                                row.subscribeorderdetail_name = row.subscribeorderdetail_name.substring(0,1)
                                $('#subscribeTable').bootstrapTable('remove',{field:'subscribeorderdetail_name',values:row.subscribeorderdetail_name});
                                $('#subscribeTable').bootstrapTable('hideRow',{index});
                                $('#subscribeTable').bootstrapTable('load',{index});
                                continue
                            }
                            _self.searchRow.splice($.inArray(row, _self.searchRow), 1)
                        }
                    });
                }
            };

//          budget
            function queryParamsBudgetGroup(params){
                params.project_id = _self.project_id;
                params.search_text = $('#searchText').val()
                return JSON.stringify(params);
            }
            function queryParamsBudgetDetail(params) {
                params.project_detail_id = _self.project_detail_id;
                params.project_space_position = $('#select_space_position').val();
                params.worker_id = $('#worker_type').val();
                return JSON.stringify(params);
            }
            function initBudgetGroupTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('project_detail_id','项目预算详情ID'),
                    common.BTRowFormatAlignLeft('roomtype_name', '户型名称'),
                    common.BTRowFormatAlignLeft('space_name', '空间名称'),
                    common.BTRowFormatWithFormatterAlignRight('space_budget_amount', '空间预算金额',common.priceFormat),
                    common.BTRowFormatEditableRight('space_count', '空间数量'),
                    common.BTRowFormatWithFormatterAlignRight('space_total_amount', '总金额',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 'space_state', '状态', 'projectBudgetState'),
                    common.BTRowFormatWithFE('', '操作', ()=>{
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 searchBudgetDetail">查看</a>' +
                            '<a class="btn btn-info btn-xs m-r-5 deleteGroup">删除</a>'
                        ].join('')
                    }, tableEvents)
                ];

                $budgetGroupTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_detail',
                    queryParams: queryParamsBudgetGroup,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.budgetState = stateFilter(res.info.rows,_self.pagePara.projectBudgetState,'space_state');
                        _self.budgetInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'project_detail_id',
                    uniqueId: 'project_detail_id',
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
                        let checkState = _self.checkProjectState('budget');
                        if(!checkState.state){
                            $budgetGroupTable.bootstrapTable('updateByUniqueId', {id: row.project_detail_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }
                        common.rowModifyWithT(_self, apiUrl + 'modify_detail', row, 'project_detail_id', $budgetGroupTable)
                    }
                });
                common.changeTableClass($budgetGroupTable)
            }
            function initBudgetDetailTable() {
                let columnArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEdSelect2(_self, 'project_space_position', '空间位置', 'spacePositionInfo'),
                common.BTRowFormatAlignLeft('project_space_name', '施工项'),
                common.BTRowFormatEdSelect2(_self, 'worker_id', '工种', 'professionInfo'),
                common.BTRowFormatEnumberWMinEnable('count', '数量'),
                common.BTRowFormat('worker_unit', '计量单位'),
                common.BTRowFormatEnumberWMinEnable('worker_budget', '人工预算单价'),
                common.BTRowFormatEnumberWMinEnable('material_budget', '材料预算单价'),
                common.BTRowFormatWithFormatterAlignRight('worker_total_budget', '人工预算总价',common.priceFormat),
                common.BTRowFormatWithFormatterAlignRight('material_total_budget', '材料预算总价',common.priceFormat),
                common.BTRowFormatWithFE('', '操作', ()=>{
                        if(_self.budgetState.id==2){
                            return [
                                '<a class="btn btn-primary btn-xs m-r-5 subscribeDetail btn-info-delete">申购</a>'
                            ].join('')
                        }
                    }, tableEvents)
                ];

                $budgetDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_space_detail',
                    queryParams: queryParamsBudgetDetail,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columnArr,
                    sortOrder:'asc',
                    idField: 'project_space_id',
                    uniqueId: 'project_space_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        let checkState = _self.checkProjectState('budget');
                        if(!checkState.state){
                            $budgetDetailTable.bootstrapTable('updateByUniqueId', {id: row.project_space_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }
                        common.rowModifyWithT(_self, apiUrl + 'modify_space', row, 'project_space_id', $budgetDetailTable)
                    }
                });
                common.changeTableClass($budgetDetailTable)
            }

//          subscribe
            function queryParamsSubscribe(params) {
                params.project_id=_self.project_id;
                params.search_text=$('#search_text_subscribe').val();
                return JSON.stringify(params)
            }
            function ncaFormatter(value, row, index) {
                return [
                    '<a class="deleteSubscribe btn btn-xs btn-info m-r-5 " style="cursor: pointer;">删除</a>'
                ].join('')
            }
            function initSubscribeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribeorderdetail_name', '物料名称'),
                    common.BTRowFormat('subscribeorderdetail_format', '规格型号'),
                    common.BTRowFormat('subscribeorderdetail_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribeorderdetail_number', '数量'),
                    common.BTRowFormatWithFormatterAlignLeft('subscribeorderdetail_remark', '备注',common.remarkFormatter),
                    common.BTRowFormatWithFE('','操作', ncaFormatter, tableEvents),
                ];
                $subscribeTable.bootstrapTable({
                    queryParams: queryParamsSubscribe,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.subscribeState = stateFilter(res.info.rows,_self.pagePara.projectSubscribeState,'subscribe_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectsubscribe_id',
                    uniqueId: 'projectsubscribe_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($subscribeTable)
            }

            // 材料采购
            function queryParamsPurchase(params) {
                params.project_id=_self.project_id;
                params.search_text=$('#search_text_purchase').val();
                return JSON.stringify(params)
            }
            function initPurchaseTable() {
                let columsArr = [
                    {field: 'state', checkbox: true},
                    common.BTRowFormatWithIndex('NO.'),
                    // common.BTRowFormat('subscribeorder_code', '申购单编号'),
                    common.BTRowFormatAlignLeft('subscribeorderdetailend_name', '物料名称'),
                    common.BTRowFormat('subscribeorderdetailend_format', '规格型号'),
                    common.BTRowFormat('subscribeorderdetailend_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribeorderdetailend_number', '申购数量'),
                    common.BTRowFormatAlignRight('purchase_done_number', '已采购数量'),
                    common.BTRowFormatEditableRight('purchaseorderdetail_number', '本次采购数量'),
                    common.BTRowFormatEditableRight('purchaseorderdetail_price', '单价'),
                ];
                $purchaseTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubcribeOrderDetailEnd',
                    queryParams: queryParamsPurchase,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectsubscribeorderdetailend_id',
                    uniqueId: 'projectsubscribeorderdetailend_id',
                    striped: true,
                    clickToSelect: false,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        if (field === 'purchaseorderdetail_number' || field === 'purchaseorderdetail_price') {
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.purchaseorderdetail_number) || !reg.test(row.purchaseorderdetail_price)) {
                                $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribeorderdetailend_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }
                        if (field === 'purchaseorderdetail_number' || field === 'purchaseorderdetail_price') {
                            let params = {
                                purchaseorderdetail_number: row.purchaseorderdetail_number,
                                projectsubscribeorderdetailend_id: row.projectsubscribeorderdetailend_id,
                            };
                            _self.$http.post(apiUrl + 'checkPurchaseDoneNumber', params).then(response => {
                                // $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribeorderdetailend_id, row: _self.oldRow});
                                $purchaseTable.bootstrapTable('updateRow', {id: row.projectsubscribeorderdetailend_id, row: row});
                            }, (response) => {
                                // $purchaseTable.bootstrapTable("refresh")
                                $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribeorderdetailend_id, row: _self.oldRow});
                                return common.dealErrorCommon(_self, response);
                            });
                        }
                    },
                    onCheckAll: function(rows) {
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchPurchaseRow.push(rows[i])
                        }
                    },
                    onUncheckAll: function(rows) {
                        _self.searchPurchaseRow = []
                    },
                    onCheck: function(row, $element) {
                        _self.searchPurchaseRow.push(row);
                    },
                    onUncheck: function(row, $element) {
                        _self.searchPurchaseRow.splice($.inArray(row, _self.searchPurchaseRow), 1);
                    }
                });
                common.changeTableClass($purchaseTable)
            }

            //材料收料
            function queryParamsReceive(params) {
                params.search_text=$('#search_text_receive').val();
                params.project_id = _self.project_id;
                return JSON.stringify(params)
            }
            function initReceiveTable() {
                let columsArr = [
                    {field: 'state', checkbox: true},
                    common.BTRowFormatWithIndex('NO.'),
                    // common.BTRowFormat('purchaseorder_code', '采购单编号'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribe_number', '已采购数量'),
                    common.BTRowFormatAlignRight('receive_done_number', '已收料数量'),
                    common.BTRowFormatAlignLeft('supplier_name', '供应商名称'),
                    common.BTRowFormatEditable('receive_now_number', '本次收料数量'),
                    common.BTRowFormatWithFormatterAlignRight('subscribe_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('receive_now_money', '金额',common.priceFormat),
                ];
                $projectReceiveTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsReceive,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.receiveInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectsubscribe_id',
                    uniqueId: 'projectsubscribe_id',
                    striped: true,
                    clickToSelect: false,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: async (field, row, oldValue, $el) => {
                        // let checkState = _self.checkProjectState();
                        // if(!checkState.state){
                        //     $projectReceiveTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                        //     return common.dealPromptCommon(checkState.message);
                        // }
                        if(field == 'receive_now_number' || field == 'receive_now_price'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.receive_now_number) || !reg.test(row.receive_now_price)) {
                                $projectReceiveTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }
                        if (field === 'receive_now_number' || field === 'subscribe_price') {
                            let params = {
                                receive_now_number: row.receive_now_number,
                                projectsubscribe_id: row.projectsubscribe_id,
                                subscribe_price: row.subscribe_price
                            };
                            _self.$http.post(apiUrl + 'checkReceiveDoneNumber', params).then(response => {
                                row.receive_now_money = Number(row.receive_now_number * row.subscribe_price).toFixed(2);
                                $projectReceiveTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: row})
                            }, (response) => {
                                $projectReceiveTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                                return common.dealErrorCommon(_self, response);
                            });
                        }else {
                            row.receive_now_money = row.receive_now_number * row.subscribe_price;
                            $projectReceiveTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: row})
                        }

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
                        let params = {}
                        params.receive_now_money = _self.receive_now_money;
                    },
                    onUncheck: function(row, $element) {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    }
                });
                common.changeTableClass($projectReceiveTable)
            }

            // 人工结算
            function queryParamsClear(params) {
                params.project_id=_self.project_id;
                params.search_text=$('#search_text_clear').val();
                params.budget_state = 2;
                return JSON.stringify(params)
            }
            function initClearTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
//                    common.BTRowFormat('budget_measurement', '计量单位'),
                    common.BTRowFormatAlignRight('budget_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_price', '人工预算单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_money', '人工预算总价',common.priceFormat),
                    common.BTRowFormatAlignRight('clearing_last_finishlimit', '上次施工完成度'),
                    common.BTRowFormatEditableRight('clearing_now_finishlimit', '本次项目完成度'),
                    common.BTRowFormatWithFormatterAlignRight('clearing_estimate_money', '预计进度款',common.priceFormat),
                    common.BTRowFormatEditableRight('clearing_reality_money', '实际进度款'),
                ];
                $projectclearTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchBudget',
                    queryParams: queryParamsClear,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.clearState = stateFilter(res.info.rows,_self.pagePara.projectClearingState,'clearing_state');
                        _self.clearingInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectbudget_id',
                    uniqueId: 'projectbudget_id',
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
                        let checkState = _self.checkProjectState('clear');
                        if(!checkState.state){
                            $projectclearTable.bootstrapTable('updateByUniqueId', {id: row.projectbudget_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }

                        if(field == 'clearing_now_finishlimit' || field == 'clearing_reality_money'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.clearing_now_finishlimit) || !reg.test(row.clearing_reality_money)) {
                                $projectclearTable.bootstrapTable('updateByUniqueId', {id: row.projectbudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }

                        if (field === 'clearing_now_finishlimit') {
                            if (parseInt(row.clearing_now_finishlimit) > 100) {
                                $projectclearTable.bootstrapTable('updateByUniqueId', {id: row.projectbudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('项目完成度不能大于100');
                            }
                            row.clearing_estimate_money = (row.budget_manual_money * (row.clearing_now_finishlimit/100)).toFixed(2);
                            row.clearing_reality_money = (row.budget_manual_money * (row.clearing_now_finishlimit/100)).toFixed(2);
                            let reg =  /^[1-9]\d*$/;
                            if(!reg.test(row.clearing_now_finishlimit)){
                                $projectclearTable.bootstrapTable('updateByUniqueId', {id: row.projectbudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('请输入正确的整数')
                            }
                        };

                        if (field === 'clearing_now_finishlimit') {
                            if(parseInt(row.clearing_now_finishlimit) < parseInt(row.clearing_last_finishlimit)){
                                $('#projectclearTable').bootstrapTable("refresh");
                                return common.dealPromptCommon('本次项目完成度不能小于上次施工完成度');
                            }else {}
                        };
                        let response = await _self.$http.post(apiUrl + 'modifyBudgetClearing', {'old': _self.oldRow, 'new': row});
                        $projectclearTable.bootstrapTable('updateByUniqueId', {id: row.projectbudget_id, row: response.data.info});

                        response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {project_id:_self.project_id});
                        _self.budgetTotal = response.data.info;
                        _self.budgetTotal.total_clearing_reality_money=_self.budgetTotal.total_clearing_reality_money.toFixed(2);
                    }
                });
                common.changeTableClass($projectclearTable)
            }

            //材料耗用
            function queryParamsConsume(params) {
                params.search_text=$('#search_text_consume').val();
                params.project_id = _self.project_id;
                params.tableIndex = _self.tableType;
                return JSON.stringify(params)
            }
            function initConsumeTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('residue_done_number', '收料数量'),
                    common.BTRowFormatEditableRight('consume_now_number', '本次耗用数量'),
                    common.BTRowFormatWithFormatterAlignRight('consume_now_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('consume_now_money', '金额',common.priceFormat),
                ];
                $projectConsumeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsConsume,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.consumeState = stateFilter(res.info.rows,_self.pagePara.projectConsumeState,'consume_state')
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectsubscribe_id',
                    uniqueId: 'projectsubscribe_id',
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
                        let checkState = _self.checkProjectState('consume');
                        if(!checkState.state){
                            $projectConsumeTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }
                        if(field == 'consume_now_number'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.consume_now_number)) {
                                $projectConsumeTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }

                            let params = {
                                consume_now_number: row.consume_now_number,
                                projectsubscribe_id: row.projectsubscribe_id
                            };
                            _self.$http.post(apiUrl + 'checkConsumeDoneNumber', params).then(response => {
                                common.rowModifyWithT(_self, apiUrl + 'modifySubscribeConsume', row, 'projectsubscribe_id', $projectConsumeTable)
                            }, (response) => {
                                $projectConsumeTable.bootstrapTable('updateByUniqueId', {id: row.projectsubscribe_id, row: _self.oldRow});
                                return common.dealErrorCommon(_self, response);
                            });
                        }
                    }
                });
                common.changeTableClass($projectConsumeTable)
            }

            // 购建费用
            function operateFormatter(value, row, index) {
                return [
                    '<a class="tableDelete btn btn-xs btn-info m-r-5" style="cursor: pointer;">删除</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
                ].join('')
            }
            function queryParamsCost(params) {
                params.project_id = _self.project_id;
                params.search_text=$('#search_text_cost').val();
                return JSON.stringify(params)
            }
            function initCostTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                        let checkState = _self.checkProjectState('cost');
                        if(!checkState.state){
                            return common.dealPromptCommon(checkState.message);
                        }

                        common.dealConfrimCommon('您确定删除么', function () {
                            _self.$http.post(apiUrl + 'deleteCost', {projectcost_id: row.projectcost_id}).then((response) => {
                                $('#projectcostTable').bootstrapTable("refresh");
                            }, (response) => {
                                return common.dealPromptCommon('删除失败');
                            });
                        });
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEditableLeft('projectcost_name', '购建费用事项'),
                    common.BTRowFormatEditableRight('projectcost_invoice_money', '有发票金额'),
                    common.BTRowFormatEditableRight('projectcost_noinvoice_money', '无发票金额'),
                    common.BTRowFormatWithFormatterAlignRight('projectcost_money','金额',common.priceFormat),
                    common.BTRowFormatWithFE('','操作', operateFormatter, tableEvents),

                ];
                $projectcostTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCost',
                    queryParams: queryParamsCost,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.costState = stateFilter(res.info.rows,_self.pagePara.projectCostState,'projectcost_state');
                        _self.costInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectcost_id',
                    uniqueId: 'projectcost_id',
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
                        let checkState = _self.checkProjectState('cost');
                        if(!checkState.state){
                            $projectcostTable.bootstrapTable('updateByUniqueId', {id: row.projectcost_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }

                        if(field == 'projectcost_invoice_money' || field == 'projectcost_noinvoice_money'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.projectcost_invoice_money) || !reg.test(row.projectcost_noinvoice_money)) {
                                $projectcostTable.bootstrapTable('updateByUniqueId', {id: row.projectcost_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }

                        let response = await _self.$http.post(apiUrl + 'modifyCost', {'old': _self.oldRow, 'new': row});
                        $projectcostTable.bootstrapTable('updateByUniqueId', {id: row.projectcost_id, row: response.data.info})
                        response = await _self.$http.post(apiUrl + 'getSumCostMoney', {project_id:_self.project_id});
                        _self.costTotal = response.data.info;
                        _self.costInfo.total_projectcost_money = _self.costTotal.total_cost_money.toFixed(2)
                        console.log(_self.costInfo.total_projectcost_money,'total_cost_money')
                    }
                });
                common.changeTableClass($projectcostTable)
            }

            // 验收移交 材料结余
            function queryParamsTranfer(params) {
                params.project_id = _self.project_id;
                params.search_text = $('#search_text_check').val();
                return JSON.stringify(params)
            }
            function initCheckTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribe_name', '物料名称'),
                    common.BTRowFormat('subscribe_format','规格型号'),
                    common.BTRowFormat('subscribe_unit', '计量单位'),
                    common.BTRowFormatAlignRight('surplus_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('surplus_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('surplus_money', '金额',common.priceFormat),
                ];
                $materialBalanceTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCheck',
                    queryParams: queryParamsTranfer,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        if(_self.costInfo.total_projectcost_money !== 0){
                            if(_self.costInfo.rows[0].projectcost_state === 2){
                                _self.checkInfo = res.info;
                            }
                        }
                        console.log(_self,'checkInfo')
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'projectsubscribe_id',
                    uniqueId: 'projectsubscribe_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                });
                common.changeTableClass($materialBalanceTable)
            }

            async function initData() {
                let response = {};

                _self.project_id = _self.$route.query.projectId;
                _self.projectsubscribeorder_id = _self.$route.query.projectsubscribeorder_id;
                if(_self.projectsubscribeorder_id){
                    response = await _self.$http.post(apiUrl + 'getProjectIdByProjectsubscribeorderId', {projectsubscribeorder_id:_self.projectsubscribeorder_id});
                    _self.project_id = response.data.info[0].project_id;
                }

                response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                console.log(_self.pagePara)
                common.initSelect2($('#space_worker_type'), _self.pagePara.professionInfo);
                common.initSelect2($('#space_position'), _self.pagePara.spacePositionInfo);

                response = await _self.$http.post(apiUrl + 'search', {project_id:_self.project_id});
                _self.projectInfo = response.data.info.rows[0];

                response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {project_id:_self.project_id});
                _self.budgetTotal = response.data.info;

                response = await _self.$http.post(apiUrl + 'getSumCostMoney', {project_id:_self.project_id});
                _self.costTotal = response.data.info;
                _self.costTotal.total_cost_money = _self.costTotal.total_cost_money.toFixed(2);
                for(let s of _self.pagePara.projectCheckState){
                    if(s.id == _self.projectInfo.project_check_state){
                        _self.checkState = s
                    }
                }

                common.initSelect2($('#supplier_id'), _self.pagePara.supplierInfo);

            }
            $(async function() {

                await initData();
                initBudgetGroupTable();
                initBudgetDetailTable();
                initSubscribeTable();
                initPurchaseTable();
                initReceiveTable();
                initConsumeTable();
                initCostTable();
                initClearTable();
                initCheckTable();
            })
        },
        methods: {
            checkProjectState:function(nowModule){
                let _self = this;
                let returnData = {state:true,message:''};
                if(_self.projectInfo.project_check_state == 1){
                    return {state:false,message:'该工程已提交验收，不能对其进行修改'}
                }
                if(_self.projectInfo.project_check_state == 2){
                    return {state:false,message:'该工程已通过验收，不能对其进行修改'}
                }

                if(nowModule == 'subscribe'){
                    if(_self.budgetState.id == 2){
                        return {state:false,message:'预算已通过，不能上传资料'}
                    }
                }
                if(nowModule == 'consume'){
                    if(_self.consumeState.id == 1){
                        return {state:false,message:'该耗用单已提交审核，不能对其进行修改'}
                    }
                    if(_self.consumeState.id == 2){
                        return {state:false,message:'该耗用单已通过，不能对其进行修改'}
                    }
                }
                if(nowModule == 'budget'){
                    if(_self.budgetState.id == 1){
                        return {state:false,message:'该预算单已提交审核，不能对其进行修改'}
                    }
                    if(_self.budgetState.id == 2){
                        return {state:false,message:'该预算单已通过，不能对其进行修改'}
                    }
                }

                if(nowModule == 'clear'){
                    if(_self.clearState.id == 1){
                        return {state:false,message:'该人工结算已提交审核，不能对其进行修改'}
                    }
                    if(_self.clearState.id == 2){
                        return {state:false,message:'该人工结算已通过，不能对其进行修改'}
                    }
                }

                if(nowModule == 'cost'){
                    if(_self.costState.id == 1){
                        return {state:false,message:'该费用单已提交审核，不能对其进行修改'}
                    }
                    if(_self.costState.id == 2){
                        return {state:false,message:'该费用单已通过，不能对其进行修改'}
                    }
                }

                return returnData
            },
            showTable:async function(index){
                let _self = this;
                $("#subscribe").removeClass("active in")
                if(index === 1 ){
                    _self.budgetGroupShow = true;
                    $('#budgetGroupTable').bootstrapTable("refresh")
                } else if (index === 1.1) {
                    $('#budgetDetailTable').bootstrapTable("refresh")
                }else if (index === 2 ){
                    _self.tableType = 9;
                    return common.dealPromptCommon('请点击购建预算的申购按钮对申购单进行材料申购');
                } else if (index === 3){
                    $('#projectReceiveTable').bootstrapTable("refresh")
                } else if (index === 4){
                    let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {project_id:_self.project_id});
                    _self.budgetTotal = response.data.info;
                    $('#projectclearTable').bootstrapTable("refresh")
                } else if (index === 5){
                    $('#projectConsumeTable').bootstrapTable("refresh")
                }else if(index === 6){
                    let response = await _self.$http.post(apiUrl + 'getSumCostMoney', {project_id:_self.project_id});
                    _self.costTotal = response.data.info;
                    $('#projectcostTable').bootstrapTable("refresh")
                }else if (index === 7) {
                    $('#materialBalanceTable').bootstrapTable("refresh")
                }else {
                    $('#purchaseTable').bootstrapTable("refresh")
                }
                _self.tableType = index;

            },
            addFinalQuotation: function(){
                let _self = this;

                let checkState = _self.checkProjectState('budget');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }

                common.dealConfrimCommon('确定提交审核？', async function () {
                    _self.project_quoted_amount = 0;
                    $('#formX').parsley().reset();
                    $('#finalQuotation').modal('show')
                });
            },
            submitClickBudgetGroup: function () {
                let _self = this;
                let param = {
                    project_id: _self.project_id,
                    project_quoted_amount: _self.project_quoted_amount
                }
                _self.$http.post(apiUrl + 'sendBudgetTask', param).then((response) => {
                    let retData = response.data;
                    if (retData.errno === 0) {

                        common.dealSuccessCommon('提交成功');
                        $('#finalQuotation').modal('hide');
                        _self.budgetGroupShow = true;
                        _self.tableType = 1;
                        $('#budgetGroupTable').bootstrapTable("refresh");

                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
            },
            addClickBudgetDetail: function () {
                let _self = this;
                let checkState = _self.checkProjectState('budget');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }

                $('#space_worker_type').val(null).trigger('change');
                $('#space_position').val(null).trigger('change');
                $('#space_name').val('');
                $('#space_count').val('');
                $('#material_budget').val('');

                $('#formProject').parsley().reset();
                $('#addBudgetDetail').modal('show')
            },
            submitProjectDetail: function() {
                let _self = this;
                if ($('#formProject').parsley().isValid()) {
                    let param = {
                        project_id: _self.project_id,
                        project_detail_id: _self.project_detail_id,
                        project_space_position: common.getSelect2Text('space_position'),
                        project_space_name: $('#space_name').val(),
                        count: $('#space_count').val(),
                        material_budget: $('#material_budget').val(),
                        worker_id: common.getSelect2Val('space_worker_type'),
                    }
                    _self.$http.post(apiUrl + 'add_space', param).then((response) => {
                        let retData = response.data
                        if (retData.errno === 0) {
                            common.dealSuccessCommon('项目创建成功')
                            $('#budgetDetailTable').bootstrapTable("refresh")
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    },(error) =>{
                        common.dealErrorCommon(_self, error)
                    })
                    $('#addBudgetDetail').modal('hide')
                }
            },
            addSubscribe: function(event) {
                let _self = this;
                _self.workRow = {};
                let checkState = _self.checkProjectState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                let formB = $('#formB');
                console.log(formB.parsley());
                $('#AddModalSubcribe').modal('show');
            },
            saveSubscribe: async function(event) {
                let _self = this;
                let formB = $('#formB');
                if (formB.parsley().isValid()) {
                    try {
                        let params = {}
                        params.subscribeorderdetail_name = _self.workRow.subscribe_name;
                        params.subscribeorderdetail_format =  _self.workRow.subscribe_format;
                        params.subscribeorderdetail_unit =  _self.workRow.subscribe_unit;
                        params.subscribeorderdetail_number =  _self.workRow.subscribe_number;
                        params.subscribeorderdetail_remark =  _self.workRow.subscribe_remark;
                        $('#subscribeTable').bootstrapTable("append",params);
                        _self.searchRow.push(params)
                        _self.workRow = {};
                        formB.parsley().reset();
                        $('#AddModalSubcribe').modal('hide');
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            submitSubscribe: async function() {
                let _self = this;
                let checkState = _self.checkProjectState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                if(_self.searchRow.length === 0){
                    return common.dealPromptCommon('申购表是空的，提交审核失败')
                }else{
                    common.dealConfrimCommon('确定提交审核？', async function () {
                        try {
                            let params = {};
                            params.project_id = _self.project_id;
                            params.projectbudget_id  = _self.project_space_id;
                            params.subscribeArr = _self.searchRow;
                            if(params.subscribeArr.length==0){

                                return common.dealPromptCommon('申购表是空的，提交审核失败')
                            }
                            let response = await _self.$http.post(apiUrl + 'sendSubscribeTask', params);
                            await $('#subscribeTable').bootstrapTable("refresh");
                            _self.searchRow = [];
                            $('#subscribeTable').bootstrapTable("removeAll");
                        } catch (error) {
                            common.dealErrorCommon(_self,error);
                        }
                    });
                }
            },
            savePurchase: async function(){
                let _self = this;
                let formE = $('#formE');
                try{
                    if(_self.searchPurchaseRow.length==0){
                        return common.dealPromptCommon('请选择物料')
                    }
                    let params = {};
                    params.project_id = _self.project_id;
                    params.supplier_id = common.getSelect2Val('supplier_id');
                    if(params.supplier_id === ''){
                        common.dealPromptCommon('请选择供应商')
                        return
                    }
                    params.purchaseArr = _self.searchPurchaseRow;
                    let response = await _self.$http.post(apiUrl + 'savePurchaseInfo', params)
                    $('#purchaseTable').bootstrapTable("refresh");
                    _self.searchPurchaseRow = [];
                    formE.parsley().reset();
                    $('#AddModalPurchase').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self,error)
                }

            },
            submitPurchase:function(){
                let _self = this;
                let checkState = _self.checkProjectState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                for(let p of _self.searchPurchaseRow){
                    if(p.purchaseorderdetail_number === 0){
                        common.dealPromptCommon('请输入本次采购数量')
                        return
                    }
                    if(p.purchaseorderdetail_price === 0){
                        common.dealPromptCommon('请输入单价')
                        return
                    }
                }
                $('#AddModalPurchase').modal('show');
                let formE = $('#formE');
                if(formE.parsley().isValid()) {
                    try {
                        let params = {};
                        params.supplier_id = common.getSelect2Val('supplier_id'),
                            params.project_id = _self.project_id;
                        for (let i of params.supplier_id) {
                            _self.searchPurchaseRow.push(params)
                        }
                    }catch (error) {
                        common.dealErrorCommon(_self,error);
                    }
                }
            },
            saveReceive: function() {
                let _self = this;
                let checkState = _self.checkProjectState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }

                if(_self.searchedRow.length==0){
                    return common.dealPromptCommon('请选择需要提交的物料')
                }
                common.dealConfrimCommon('确定提交收料？', async function () {
                    try {
                        for(let a of _self.searchedRow){
                            if(a.receive_now_number === 0){
                                common.dealPromptCommon('本次收料数量未更改，不能提交收料单')
                                return
                            }else {}
                        }
                        let params = {};
                        params.project_id = _self.project_id;
                        params.receiveDetailItem = _self.searchedRow;
                        let response = await _self.$http.post(apiUrl + 'addReceive', params);
                        $('#projectReceiveTable').bootstrapTable("refresh")
                        _self.searchedRow = []
                    }catch (error) {
                        common.dealErrorCommon(_self,error)
                    }
                });
            },
            submitConsume: function(){
                let _self = this;
                let checkState = _self.checkProjectState('consume');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                common.dealConfrimCommon('确定提交耗用审核？', async function () {
                    try{
                        let params = {};
                        params.project_id = _self.project_id;
                        params.project_name = _self.projectInfo.project_name;
                        let response = await _self.$http.post(apiUrl + 'sendConumeTask', params)
                        $('#projectConsumeTable').bootstrapTable("refresh")
                    } catch (error) {
                        common.dealErrorCommon(_self,error)
                    }
                });
            },
            AddModalCost: function (event) {
                let _self = this;
                _self.workRow = {};
                let checkState = _self.checkProjectState('cost');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                $('#AddModalCost').modal('show')
            },
            saveCost: async function(event){
                let _self = this;
                let formD = $('#formD');
                if(formD.parsley().isValid()) {
                    try {
                        let params = {};
                        params.projectcost_name = _self.workRow.projectcost_name;
                        params.projectcost_invoice_money = _self.workRow.projectcost_invoice_money?_self.workRow.projectcost_invoice_money:0;
                        params.projectcost_noinvoice_money = _self.workRow.projectcost_noinvoice_money?_self.workRow.projectcost_noinvoice_money:0;
                        params.project_id = _self.project_id;
                        let response = await _self.$http.post(apiUrl + 'addCost', params);
                        $('#projectcostTable').bootstrapTable("refresh");
                        _self.workRow = {};
                        formD.parsley().reset();
                        $('#AddModalCost').modal('hide');
                    }catch (error) {
                        common.dealErrorCommon(_self,error);
                    }
                }
            },
            submitCost: async function(){
                let _self = this;
                let checkState = _self.checkProjectState('cost');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                if(_self.costState.id === undefined){
                    common.dealPromptCommon('购建费用单未新增，不能提交审核')
                    return
                }else {
                    common.dealConfrimCommon('确认提交审核？', async function () {
                        try {
                            let params = {}
                            params.project_id = _self.project_id;
                            params.project_name = _self.projectInfo.project_name;
                            let response = await _self.$http.post(apiUrl + 'sendCostTask', params);
                            $('#projectcostTable').bootstrapTable("refresh")
                        } catch(error){
                            common.dealErrorCommon(_self,error)
                        }
                    });
                }
            },
            submitClear: function(){
                let _self=this;
                let checkState = _self.checkProjectState('clear');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                let i = 0;
                for (let r of _self.clearingInfo.rows){
                    if(r.clearing_last_finishlimit === 100){
                        i++
                        if (i < _self.clearingInfo.total){
                            console.log('i',i)
                        }else {
                            common.dealPromptCommon('本条施工项的施工已完成，不能继续提交审核')
                            return
                        }
                    }else {
                        common.dealConfrimCommon('确定提交审核?', async function () {
                            try {
                                let params = {};
                                params.project_id = _self.project_id;
                                params.project_name = _self.projectInfo.project_name;
                                let response = await _self.$http.post(apiUrl + 'sendClearingTask', params);
                                $('#projectclearTable').bootstrapTable("refresh")
                            } catch (error) {
                                common.dealErrorCommon(_self,error)
                            }
                        });
                        return
                    }
                }
            },
            submitCheck: async function(){
                let _self = this;
                let checkState = _self.checkProjectState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                // console.log(_self.checkInfo.rows[0].surplus_number)
                // for(let r of _self.clearingInfo.rows){
                //     if (r.clearing_last_finishlimit < 100){
                //         common.dealPromptCommon('人工结算中上次施工完成度未达到100，不能提交验收')
                //         return
                //     }
                // }
                if (_self.budgetState.id != 2){
                    if (_self.budgetState.id == undefined){
                        common.dealPromptCommon('购建预算未创建数据，不能提交验收')
                        return
                    }else {
                        common.dealPromptCommon('购建预算单未通过，不能提交验收')
                        return
                    }
                }else if (_self.costState.id != 2){
                    if (_self.costState.id == undefined){
                        common.dealPromptCommon('购建费用未创建数据，不能提交验收')
                        return
                    }else {
                        common.dealPromptCommon('购建费用单未通过，不能提交验收')
                        return
                    }
                }else{
                    common.dealConfrimCommon('确认提交审核?', async function() {
                        try{
                            let params ={};
                            params.project_id = _self.project_id;
                            params.project_name = _self.projectInfo.project_name;
                            let response = await _self.$http.post(apiUrl + 'sendCheckTask',params);
                            $('#materialBalanceTable').bootstrapTable("refresh");
                            _self.checkState.text = '已提交'
                        } catch (error) {
                            common.dealErrorCommon(_self,error)
                        }
                    });
                }
            },

            downloadTemp: function(type) {
                if(type == 1){
                    window.location.href = '/static/baseDataTemplate/NCAProjectBudget.xlsx';
                }else if (type == 2){
                    window.location.href = '/static/baseDataTemplate/NCAProjectSubscribe.xlsx';
                }

            },
            importData: function(type) {
                let _self = this;
                this.dataType = type;
                if(this.dataType == 1){
                    let checkState = _self.checkProjectState();
                    checkState = _self.checkProjectState('subscribe');
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }
                    console.log(this.dataType,'this.dataType9')
                }else{
                    let checkState = _self.checkProjectState();
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }
                    console.log(this.dataType,'this.dataType0')
                }
                $('#ImportModal').modal('show');
                $('#file').val('');
            },
            importAct: async function(event) {
                let _self = this;
                if (!this.fileUrl) {
                    return common.dealWarningCommon('请选择文件')
                }
                const params = {
                    uploadurl: this.fileUrl,
                    project_id:this.project_id,
                    projectbudget_id:this.projectbudget_id,
                    type:this.dataType
                };

                this.$http.post(apiUrl + 'importData', params).then((response) => {
                    const {successNumber, errorNumber} = response.data.info;
                    if ((successNumber || successNumber === 0) && (errorNumber || errorNumber === 0)) {
                        common.dealSuccessCommon(`${successNumber}个成功 ${errorNumber}个失败`);
                        $('#budgetTable').bootstrapTable("refresh");
                    } else {
                        common.dealSuccessCommon('保存成功');
                        $('#subscribeTable').bootstrapTable("refresh");
                    }
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });

                $('#ImportModal').modal('hide')
                let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {project_id:_self.project_id});
                console.log(response,'response1')
                response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {project_id:_self.project_id});
                console.log(response,'response2')
                _self.budgetTotal = response.data.info;
                _self.budgetTotal.total_budget_money = _self.budgetTotal.total_budget_money.toFixed(2)
            }
        }
    }

</script>
<style scoped>
    .panel-height{
        height: 3em;
    }
    .span1{
        font-size: 1.2em;
    }
    .margin-left{
        margin-right: 2em;
    }
    .padding-left{
        padding-left: 2em;
    }
</style>
