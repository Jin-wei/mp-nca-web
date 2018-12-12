<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">待摊资产管理</a></li>
            <li class="active">待摊资产项目详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading panel-height">
                        <h4 class="panel-title pull-left">待摊资产项目详情</h4>
                        <h4 class="panel-title pull-right ">项目名称：{{amortizeInfo.amortize_name}}</h4>
                        <h4 class="panel-title pull-right margin-left">项目编号：{{amortizeInfo.amortize_code}}</h4>
                    </div>
                    <div class="panel-toolbar">
                        <!--<div class="form-inline">-->
                            <!--构建预算-->
                            <div class="form-inline" v-show="tableType==1">
                                <div class="form-group">
                                    <button id="submitBudget" class="btn btn-info" v-on:click="submitBudget">提交</button>
                                </div>
                                <div class="form-group">
                                    <button id="addBudget" class="btn btn-info" v-on:click="addBudget">增加</button>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="downloadTemp(1)">下载模板</button>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="importData(1)">上传资料</button>
                                </div>
                                <div class="form-group pull-right" >
                                    <span class="span1">购建预算状态: {{budgetState.text}}</span>
                                    <span class="span1 padding-left">预算金额: {{_self.budgetTotal.total_budget_money}}</span>
                                </div>
                            </div>
                            <!--材料申购-->
                            <div class="form-inline" v-show="tableType==2">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_subscribe" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="showTable(2)"><i class="fa fa-search"></i></button>
                                </div>
                                <div class="form-group pull-right">
                                    <div class="form-group">
                                        <button id="submitSubscribe" class="btn btn-info" v-on:click="submitSubscribe">提交</button>
                                    </div>
                                    <div class="form-group" >
                                        <button id="addSubscribe" class="btn btn-info" v-on:click="addSubscribe">增加</button>
                                    </div>
                                    <div class="form-group">
                                        <button class="btn btn-info" v-on:click="downloadTemp(2)">下载模板</button>
                                    </div>
                                    <div class="form-group">
                                        <button class="btn btn-info" v-on:click="importData(2)">上传资料</button>
                                    </div>
                                </div>
                                <!--<div class="pull-right">-->
                                    <!--<span class="span1">申购单状态: {{subscribeState.text}}</span>-->
                                <!--</div>-->
                            </div>
                            <!--材料采购-->
                            <div class="form-inline" v-show="tableType==8">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_purchase" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="searchPurchase" class="btn btn-info" v-on:click="showTable(8)"><i class="fa fa-search"></i></button>
                                </div>
                                <div class="form-group pull-right">
                                    <button id="submitPurchase" class="btn btn-info" v-on:click="submitPurchase">提交</button>
                                </div>
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
                                <div class="form-group pull-right">
                                    <button id="submitReceive" class="btn btn-info" v-on:click="saveReceive">提交</button>
                                </div>
                            </div>
                            <!--人工结算-->
                            <div class="form-inline" v-show="tableType==4">
                                <!--<div class="form-inline">    -->
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_clear" placeholder="搜索施工名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="searchClear" class="btn btn-info" v-on:click="showTable(4)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <div class="form-group">
                                        <button id="submitClear" class="btn btn-info" v-on:click="submitClear">提交</button>
                                    </div>
                                    <div class="pull-right">
                                        <span class="span1">结算状态: {{clearState.text}}</span>
                                        <span class="span1 padding-left">总金额: {{ _self.budgetTotal.total_clearing_reality_money}}</span>
                                    </div>
                                <!--</div>-->
                            </div>
                            <!--材料耗用-->
                            <div class="form-inline" v-show="tableType==5">
                                <!--<div class="form-inline">-->
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_consume" placeholder="搜索物料名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_consume" class="btn btn-info" v-on:click="showTable(5)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <div class="form-group">
                                        <button id="submitConsume" class="btn btn-info" v-on:click="submitConsume">提交</button>
                                    </div>
                                    <div class="pull-right">
                                        <span class="span1">
                                            耗用单状态：{{consumeState.text}}
                                        </span>
                                    </div>
                                <!--</div>-->
                            </div>
                            <!--构建费用-->
                            <div class="form-inline" v-show="tableType==6">
                                <!--<div class="form-inline">-->
                                    <div class="form-group">
                                        <input class="form-control"  id="search_text_cost" placeholder="搜索购建费用事项名称" style="width: 230px;">
                                    </div>
                                    <div class="form-group">
                                        <button id="search_cost" class="btn btn-info" v-on:click="showTable(6)"><i class="fa fa-search"></i></button>
                                    </div>
                                    <div class="form-group ">
                                        <button id="submitCost" class="btn btn-info" v-on:click="submitCost">提交</button>
                                    </div>
                                    <div class="form-group">
                                        <button id="addModalBudCost" class="btn btn-info" v-on:click="AddModalCost">增加</button>
                                    </div>
                                    <div class="form-group pull-right">
                                        <span class="span1">费用单状态：{{costState.text}}</span>
                                        <span class="span1 padding-left">合计：{{_self.costInfo.total_amortizecost_money}}</span>
                                    </div>
                                <!--</div>-->
                            </div>
                            <!--移交验收-->
                            <div class="form-inline" v-show="tableType==7">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_check" placeholder="搜索物料名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search_check" class="btn btn-info" v-on:click="showTable(7)"><i class="fa fa-search"></i></button>
                                </div>
                                <div class="form-group ">
                                    <button id="" class="btn btn-info" v-on:click="submitCheck">提交</button>
                                </div>
                                <div class="form-group pull-right">
                                    <span class="span1">
                                        验收状态：{{_self.checkState.text}}
                                    </span>
                                </div>
                            </div>
                        <!--</div>-->
                    </div>
                    <div class="panel-body ">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="showTable(1)" id="name1">
                                <a href="#budget" data-toggle="tab">
                                    <span>购建预算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(2)" id="name">
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
                                <a href="#amortizereceive" data-toggle="tab">
                                    <span>材料收料</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(4)">
                                <a href="#amortizeclear" data-toggle="tab">
                                    <span>人工结算</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(5)">
                                <a href="#amortizeconsume" data-toggle="tab">
                                    <span>材料耗用</span>
                                </a>
                            </li>
                            <li class="" @click="showTable(6)">
                                <a href="#amortizecost" data-toggle="tab">
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
                                <table id="budgetTable"></table>
                            </div>
                            <div class="tab-pane fade" id="subscribe">
                                <table id="subscribeTable"></table>
                            </div>
                            <div class="tab-pane fade" id="purchase">
                                <table id="purchaseTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizereceive">
                                <table id="amortizeReceiveTable"></table>
                            </div>
                            <div class="tab-pane fade" id="amortizeclear">
                                <table id="amortizeclearTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizeconsume">
                                <table id="amortizeConsumeTable"></table>
                            </div>
                            <div class="tab-pane fade " id="amortizecost">
                                <table id="amortizecostTable"></table>
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
                                                        <input class="form-control" id="totalAmortizeCostMoney" v-model="checkInfo.totalAmortizeCostMoney" readonly>
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
        <div class="modal fade" id="AddModalBudget" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增施工项记录</h4>
                    </div>
                    <form @submit.prevent="saveBudget" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>施工项名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="budget_work_name" v-model="workRow.budget_work_name" data-parsley-group="block-1" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>材料预算单价</label>
                                    <div class="col-sm-8" style=" padding-left: 11px;">
                                        <input class="form-control select2" id="budget_materiel_price" v-model="workRow.budget_materiel_price"  data-parsley-required="true" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                               oninput="if(value.length>7)value=value.slice(0,7)">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>数量</label>
                                    <div class="col-sm-8">
                                        <input class="form-control select2" v-model="workRow.budget_number" multiple style="width:100%" id="budget_number"  data-parsley-group="block-1" data-parsley-type="number" data-parsley-required="true" maxlength="80" data-parsley-maxlength="80">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>人工预算单价</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="budget_manual_price" v-model="workRow.budget_manual_price" data-parsley-group="block-1"  data-parsley-required="true" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                               oninput="if(value.length>7)value=value.slice(0,7)">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">计量单位</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.budget_measurement" id="budget_measurement" data-parsley-group="block-1" maxlength="10" data-parsley-maxlength="10">
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
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>数量</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="subscribe_number" v-model="workRow.subscribe_number" data-parsley-group="block-1" data-parsley-type="number" data-parsley-required="true">
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
                                    <label class="col-sm-4 control-label">规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.subscribe_format" id="subscribe_format" data-parsley-group="block-1" >
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10" style=" padding-left: 11px;">
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
                                        <input class="form-control" id="amortizecost_name" v-model="workRow.amortizecost_name" data-parsley-group="block-1" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-5">
                                    <label class="col-sm-4 control-label" style="padding-right: 0">有发票金额</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.amortizecost_invoice_money" id="amortizecost_invoice_money" data-parsley-group="block-1" type="text" data-parsley-required="true" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                               oninput="if(value.length>7)value=value.slice(0,7)">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-7">
                                    <label class="col-sm-5 control-label">无发票金额</label>
                                    <div class="col-sm-7">
                                        <input class="form-control select2" v-model="workRow.amortizecost_noinvoice_money" multiple style="width:100%" id="amortizecost_noinvoice_money"  data-parsley-group="block-1" type="text" data-parsley-required="true"  onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
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

        <!--上传-->
        <div class="modal fade" id="ImportModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">导入数据</h4>
                    </div>

                    <div class="modal-body">
                        <div class="form-group col-md-12">
                            <div>
                                <input type="file" id="file" class="form-control input-sm " size="1">
                                <input type="text" id="select1" hidden>
                            </div>
                        </div>

                    </div>
                    <div class="modal-footer" style="border-top:none">
                        <button type="button" class="btn btn-primary btn-info" id="importAct" v-on:click="importAct"><i class="fa fa-fw fa-plus"></i>导入</button>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeDetailControl?method=';
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                workRow:{},
                searchedRow:[],
                amortizeInfo:[],
                amortize_id: '',
                oldRow:{},
                index: '',

                subscribeState:'',
                consumeState:'',
                costState:'',
                clearState:'',
                checkState:'',
                budgetState:'',

                checkInfo:'',
                clearingInfo:'',
                clearing_last_finishlimit:'',
                budgetTotal:{},
                costTotal:{},
                receiverInfo:[],
                r:{},
                costInfo:'',
                budgetInfo:[],
                searchRow:[],
                searchPurchaseRow:[],
                dataType :''
            }
        },
        name: 'NCAAmortizeDetailControl',
        mounted: function() {
            let _self = this;

            _self.amortize_id = _self.$route.query.amortize_id;
            let $budgetTable = $('#budgetTable');
            let $subscribeTable = $('#subscribeTable');
            let $amortizeReceiveTable = $('#amortizeReceiveTable');
            let $amortizeConsumeTable = $('#amortizeConsumeTable');
            let $amortizecostTable = $('#amortizecostTable');
            let $amortizeclearTable = $('#amortizeclearTable');
            let $materialBalanceTable =$('#materialBalanceTable');
            let $purchaseTable = $('#purchaseTable');


            $('#file').change(function() {
                let files = this.files;
                let formData = new FormData();
                if(files.length>0){
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.fileUrl = response.data.info.uploadurl.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });


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

            //购建预算

            function subFormatter(value, row, index) {
                if (row.budget_state === 2){
                    return [
                        '<a class="tableSubscribe btn btn-xs btn-info m-r-5 " style="cursor: pointer;">申购</a>',
                        // '<a class="btn btn-primary btn-xs m-r-5 tableSubscribe btn-info-delete">申购</a>'
                    ].join('')
                }
            }
            function queryParamsBudget(params) {
                params.amortize_id=_self.amortize_id;
                return JSON.stringify(params)
            }
            function initBudgetTable() {
                window.tableEvents = {
                    'click .tableSubscribe': function(e, value, row, index) {
                        _self.amortizebudget_id = row.amortizebudget_id;
                        console.log('_self.amortizebudget_id ',_self.amortizebudget_id );
                        index = 2
                        _self.tableType = index
                        _self.searchRow = [];
                        // jQuery动态添加，删除class样式
                        $("#name").addClass("active")
                        $("#name1").removeClass("active")
                        $("#subscribe").addClass("active in");
                        $("#budget").removeClass("active in")
                        _self.searchRow = [];
                        $('#subscribeTable').bootstrapTable("refresh")

                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEditableLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormatEditable('budget_measurement', '计量单位'),
                    common.BTRowFormatEditableRight('budget_number', '数量'),
                    common.BTRowFormatEditableRight('budget_manual_price', '人工预算单价'),
                    common.BTRowFormatEditableRight('budget_materiel_price', '材料预算单价'),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_money', '人工预算总价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_materiel_money', '材料预算总价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_total_money', '总金额',common.priceFormat),
                    common.BTRowFormatWithFE('', '操作', subFormatter, tableEvents)
                ];
                $budgetTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchBudget',
                    queryParams: queryParamsBudget,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.budgetState = stateFilter(res.info.rows,_self.pagePara.amortizeBudgetState,'budget_state');
                        _self.budgetInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizebudget_id',
                    uniqueId: 'amortizebudget_id',
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
                        let checkState = _self.checkAmortizeState('budget');
                        if(!checkState.state){
                            $budgetTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }

                        if(field == 'budget_number' || field == 'budget_manual_price' || field == 'budget_materiel_price'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.budget_number) || !reg.test(row.budget_manual_price) || !reg.test(row.budget_materiel_price)) {
                                $budgetTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }

                        let response = await _self.$http.post(apiUrl + 'modifyBudget', {'old': _self.oldRow, 'new': row});
                        $budgetTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: response.data.info});

                        response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                        _self.budgetTotal = response.data.info;
                    }
                });
                common.changeTableClass($budgetTable)
            }

            //材料申购
            function queryParamsSubscribe(params) {
                params.amortize_id=_self.amortize_id;
                params.search_text=$('#search_text_subscribe').val();
                return JSON.stringify(params)
            }
            function ncaFormatter(value, row, index) {
                    return [
                        '<a class="tableDelete btn btn-xs btn-info m-r-5 " style="cursor: pointer;">删除</a>',
                        // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
                    ].join('')
            }
            function initSubscribeTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                        common.dealConfrimCommon('您确定删除吗？', function () {
                            console.log(_self.searchRow.length,'changdu')
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
                }
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('subscribeorderdetail_name', '物料名称'),
                    common.BTRowFormat('subscribeorderdetail_format', '规格型号'),
                    common.BTRowFormat('subscribeorderdetail_unit', '计量单位'),
                    common.BTRowFormatAlignRight('subscribeorderdetail_number', '数量'),
                    common.BTRowFormatAlignLeft('subscribeorderdetail_remark', '备注',common.remarkFormatter),
                    common.BTRowFormatWithFE('','操作', ncaFormatter, tableEvents),
                ];
                $subscribeTable.bootstrapTable({
                    method: 'POST',
                    // url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsSubscribe,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.subscribeState = stateFilter(res.info.rows,_self.pagePara.amortizeSubscribeState,'subscribe_state');
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
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
                params.amortize_id=_self.amortize_id;
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
                    idField: 'amortizesubscribeorderdetailend_id',
                    uniqueId: 'amortizesubscribeorderdetailend_id',
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
                                $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribeorderdetailend_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }
                        if (field === 'purchaseorderdetail_number' || field === 'purchaseorderdetail_price') {
                            let params = {
                                purchaseorderdetail_number: row.purchaseorderdetail_number,
                                amortizesubscribeorderdetailend_id: row.amortizesubscribeorderdetailend_id,
                            };
                                _self.$http.post(apiUrl + 'checkPurchaseDoneNumber', params).then(response => {
                                    // $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribeorderdetailend_id, row: _self.oldRow});
                                    $purchaseTable.bootstrapTable('updateRow', {id: row.amortizesubscribeorderdetailend_id, row: row});
                                }, (response) => {
                                    // $purchaseTable.bootstrapTable("refresh")
                                    $purchaseTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribeorderdetailend_id, row: _self.oldRow});
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
                params.amortize_id = _self.amortize_id;
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
                    common.BTRowFormatEditableRight('receive_now_number', '本次收料数量'),
                    common.BTRowFormatWithFormatterAlignRight('subscribe_price', '单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('receive_now_money', '金额',common.priceFormat),
                ];
                $amortizeReceiveTable.bootstrapTable({
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
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
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
                        // let checkState = _self.checkAmortizeState();
                        // if(!checkState.state){
                        //     $amortizeReceiveTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                        //     return common.dealPromptCommon(checkState.message);
                        // }
                        if(field == 'receive_now_number' || field == 'receive_now_price'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.receive_now_number) || !reg.test(row.receive_now_price)) {
                                $amortizeReceiveTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }
                        if (field === 'receive_now_number' || field === 'subscribe_price') {
                            let params = {
                                receive_now_number: row.receive_now_number,
                                amortizesubscribe_id: row.amortizesubscribe_id,
                                subscribe_price: row.subscribe_price
                            };
                            _self.$http.post(apiUrl + 'checkReceiveDoneNumber', params).then(response => {
                                row.receive_now_money = Number(row.receive_now_number * row.subscribe_price).toFixed(2);
                                $amortizeReceiveTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: row})
                            }, (response) => {
                                $amortizeReceiveTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                                return common.dealErrorCommon(_self, response);
                            });
                        }else {
                            row.receive_now_money = row.receive_now_number * row.subscribe_price;
                            $amortizeReceiveTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: row})
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
                common.changeTableClass($amortizeReceiveTable)
            }

            // 人工结算
            function queryParamsClear(params) {
                params.amortize_id=_self.amortize_id;
                params.search_text=$('#search_text_clear').val();
                params.budget_state = 2;
                return JSON.stringify(params)
            }
            function initClearTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('budget_work_name', '施工项名称'),
                    common.BTRowFormat('budget_measurement', '计量单位'),
                    common.BTRowFormatAlignRight('budget_number', '数量'),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_price', '人工预算单价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('budget_manual_money', '人工预算总价',common.priceFormat),
                    common.BTRowFormatAlignRight('clearing_last_finishlimit', '上次施工完成度'),
                    common.BTRowFormatEditableRight('clearing_now_finishlimit', '本次项目完成度'),
                    common.BTRowFormatWithFormatterAlignRight('clearing_estimate_money', '预计进度款',common.priceFormat),
                    common.BTRowFormatEditableRight('clearing_reality_money', '实际进度款'),
                ];
                $amortizeclearTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchBudget',
                    queryParams: queryParamsClear,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.clearState = stateFilter(res.info.rows,_self.pagePara.amortizeClearingState,'clearing_state');
                        _self.clearingInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizebudget_id',
                    uniqueId: 'amortizebudget_id',
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
                        let checkState = _self.checkAmortizeState('clear');
                        if(!checkState.state){
                            $amortizeclearTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }

                        if(field == 'clearing_now_finishlimit' || field == 'clearing_reality_money'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.clearing_now_finishlimit) || !reg.test(row.clearing_reality_money)) {
                                $amortizeclearTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }

                        if (field === 'clearing_now_finishlimit') {
                            if (parseInt(row.clearing_now_finishlimit) > 100) {
                                $amortizeclearTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('项目完成度不能大于100');
                            }
                            row.clearing_estimate_money = (row.budget_manual_money * (row.clearing_now_finishlimit/100)).toFixed(2);
                            row.clearing_reality_money = (row.budget_manual_money * (row.clearing_now_finishlimit/100)).toFixed(2);
                            let reg =  /^[1-9]\d*$/;
                            if(!reg.test(row.clearing_now_finishlimit)){
                                $amortizeclearTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: _self.oldRow});
                                return common.dealPromptCommon('请输入正确的整数')
                            }
                        };

                        if (field === 'clearing_now_finishlimit') {
                            if(parseInt(row.clearing_now_finishlimit) < parseInt(row.clearing_last_finishlimit)){
                                $('#amortizeclearTable').bootstrapTable("refresh");
                                return common.dealPromptCommon('本次项目完成度不能小于上次施工完成度');
                            }else {}
                        };
                        let response = await _self.$http.post(apiUrl + 'modifyBudgetClearing', {'old': _self.oldRow, 'new': row});
                        $amortizeclearTable.bootstrapTable('updateByUniqueId', {id: row.amortizebudget_id, row: response.data.info});

                        response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                        _self.budgetTotal = response.data.info;
                        _self.budgetTotal.total_clearing_reality_money=_self.budgetTotal.total_clearing_reality_money.toFixed(2);
                    }
                });
                common.changeTableClass($amortizeclearTable)
            }

            //材料耗用
            function queryParamsConsume(params) {
                params.search_text=$('#search_text_consume').val();
                params.amortize_id = _self.amortize_id;
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
                $amortizeConsumeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchSubscribe',
                    queryParams: queryParamsConsume,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.consumeState = stateFilter(res.info.rows,_self.pagePara.amortizeConsumeState,'consume_state')
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
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
                        let checkState = _self.checkAmortizeState('consume');
                        if(!checkState.state){
                            $amortizeConsumeTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }
                        if(field == 'consume_now_number'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.consume_now_number)) {
                                $amortizeConsumeTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }

                            let params = {
                                consume_now_number: row.consume_now_number,
                                amortizesubscribe_id: row.amortizesubscribe_id
                            };
                            _self.$http.post(apiUrl + 'checkConsumeDoneNumber', params).then(response => {
                                common.rowModifyWithT(_self, apiUrl + 'modifySubscribeConsume', row, 'amortizesubscribe_id', $amortizeConsumeTable)
                            }, (response) => {
                                $amortizeConsumeTable.bootstrapTable('updateByUniqueId', {id: row.amortizesubscribe_id, row: _self.oldRow});
                                return common.dealErrorCommon(_self, response);
                            });
                        }
                    }
                });
                common.changeTableClass($amortizeConsumeTable)
            }

            // 购建费用
            function operateFormatter(value, row, index) {
                return [
                    '<a class="tableDelete btn btn-xs btn-info m-r-5" style="cursor: pointer;">删除</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
                ].join('')
            }
            function queryParamsCost(params) {
                params.amortize_id = _self.amortize_id;
                params.search_text=$('#search_text_cost').val();
                return JSON.stringify(params)
            }
            function initCostTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                        let checkState = _self.checkAmortizeState('cost');
                        if(!checkState.state){
                            return common.dealPromptCommon(checkState.message);
                        }

                        common.dealConfrimCommon('您确定删除么', function () {
                            _self.$http.post(apiUrl + 'deleteCost', {amortizecost_id: row.amortizecost_id}).then((response) => {
                                $('#amortizecostTable').bootstrapTable("refresh");
                            }, (response) => {
                                return common.dealPromptCommon('删除失败');
                            });
                        });
                    }
                };
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('amortizecost_name', '购建费用事项'),
                    common.BTRowFormatEditableRight('amortizecost_invoice_money', '有发票金额'),
                    common.BTRowFormatEditableRight('amortizecost_noinvoice_money', '无发票金额'),
                    common.BTRowFormatWithFormatterAlignRight('amortizecost_money','金额',common.priceFormat),
                    common.BTRowFormatWithFE('','操作', operateFormatter, tableEvents),

                ];
                $amortizecostTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCost',
                    queryParams: queryParamsCost,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.costState = stateFilter(res.info.rows,_self.pagePara.amortizeCostState,'amortizecost_state');
                        _self.costInfo = res.info;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizecost_id',
                    uniqueId: 'amortizecost_id',
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
                        let checkState = _self.checkAmortizeState('cost');
                        if(!checkState.state){
                            $amortizecostTable.bootstrapTable('updateByUniqueId', {id: row.amortizecost_id, row: _self.oldRow});
                            return common.dealPromptCommon(checkState.message);
                        }

                        if(field == 'amortizecost_invoice_money' || field == 'amortizecost_noinvoice_money'){
                            let reg = /^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[0-9][0-9]*))$/;
                            if (!reg.test(row.amortizecost_invoice_money) || !reg.test(row.amortizecost_noinvoice_money)) {
                                $amortizecostTable.bootstrapTable('updateByUniqueId', {id: row.amortizecost_id, row: _self.oldRow});
                                return common.dealPromptCommon('只能输入大于0的数字');
                            }
                        }

                        let response = await _self.$http.post(apiUrl + 'modifyCost', {'old': _self.oldRow, 'new': row});
                        $amortizecostTable.bootstrapTable('updateByUniqueId', {id: row.amortizecost_id, row: response.data.info})
                        response = await _self.$http.post(apiUrl + 'getSumCostMoney', {amortize_id:_self.amortize_id});
                        _self.costTotal = response.data.info;
                        _self.costInfo.total_amortizecost_money = _self.costTotal.total_cost_money.toFixed(2)
                        console.log(_self.costInfo.total_amortizecost_money,'total_cost_money')
                    }
                });
                common.changeTableClass($amortizecostTable)
            }

            // 验收移交 材料结余
            function queryParamsTranfer(params) {
                params.amortize_id = _self.amortize_id;
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
                        if(_self.costInfo.total_amortizecost_money !== 0){
                            if(_self.costInfo.rows[0].amortizecost_state === 2){
                                _self.checkInfo = res.info;
                            }
                        }
                        console.log(_self,'checkInfo')
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortizesubscribe_id',
                    uniqueId: 'amortizesubscribe_id',
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

                response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                let retData = response.data.info;

                response = await _self.$http.post(apiUrl + 'getAmortize', {amortize_id:_self.amortize_id});
                _self.amortizeInfo = response.data.info[0];

                response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                _self.budgetTotal = response.data.info;

                response = await _self.$http.post(apiUrl + 'getSumCostMoney', {amortize_id:_self.amortize_id});
                _self.costTotal = response.data.info;
                _self.costTotal.total_cost_money = _self.costTotal.total_cost_money.toFixed(2)
                for(let s of _self.pagePara.amortizeCheckState){
                    if(s.id == _self.amortizeInfo.amortize_check_state){
                        _self.checkState = s
                    }
                }

                common.initSelect2($('#supplier_id'), retData.supplierInfo);
            }
            $(function() {
                initData();
                initBudgetTable();
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
            checkAmortizeState:function(nowModule){
                let _self = this;
                let returnData = {state:true,message:''};
                if(_self.amortizeInfo.amortize_check_state == 1){
                    return {state:false,message:'该待摊资产项目已提交验收，不能对其进行修改'}
                }
                if(_self.amortizeInfo.amortize_check_state == 2){
                    return {state:false,message:'该待摊资产项目已通过验收，不能对其进行修改'}
                }

                if(nowModule == 'subscribe'){
                    // if(_self.subscribeState.id == 1){
                    //     return {state:false,message:'该申购单已提交审核，不能对其进行修改'}
                    // }
                    // if(_self.subscribeState.id == 2){
                    //     return {state:false,message:'该申购单已通过，不能对其进行修改'}
                    // }

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
                $("#subscribe").removeClass("active in")
                let _self = this;
                _self.tableType=index;
                if(index === 1 ){
                    let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    _self.budgetTotal = response.data.info;
                    $('#budgetTable').bootstrapTable("refresh");
                } else if (index === 2 ){
                    common.dealPromptCommon('请点击购建预算的申购按钮对申购单进行材料申购')
                    _self.tableType == 9
                    console.log(_self.tableType = 9)
                } else if (index === 3){
                    $('#amortizeReceiveTable').bootstrapTable("refresh")
                } else if (index === 4){
                    let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                    _self.budgetTotal = response.data.info;
                    $('#amortizeclearTable').bootstrapTable("refresh")
                } else if (index === 5){
                    $('#amortizeConsumeTable').bootstrapTable("refresh")
                }else if(index === 6){
                    let response = await _self.$http.post(apiUrl + 'getSumCostMoney', {amortize_id:_self.amortize_id});
                    _self.costTotal = response.data.info;
                    $('#amortizecostTable').bootstrapTable("refresh")
                }else if (index === 7) {
                    $('#materialBalanceTable').bootstrapTable("refresh")
                }else {
                    $('#purchaseTable').bootstrapTable("refresh")
                }
            },

            addBudget: async function() {
                let _self = this;
                let checkState = _self.checkAmortizeState('budget');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                _self.workRow = {};
                $('#AddModalBudget').modal('show');
            },
            saveBudget: async function(event) {
                let _self = this;
                let formA = $('#formA');
                if (formA.parsley().isValid()) {
                    try {
                        let params = {};
                        params.budget_work_name =_self.workRow.budget_work_name;
                        params.budget_number = _self.workRow.budget_number;
                        params.budget_measurement = _self.workRow.budget_measurement;
                        params.budget_manual_price = _self.workRow.budget_manual_price;
                        params.budget_materiel_price = _self.workRow.budget_materiel_price;
                        params.amortize_id = _self.$route.query.amortize_id;

                        let response = await _self.$http.post(apiUrl + 'addBudget', params);
                        $('#budgetTable').bootstrapTable("refresh");
                        _self.workRow = {};
                        formA.parsley().reset();
                        $('#AddModalBudget').modal('hide');
                        response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                        _self.budgetTotal.total_budget_money = response.data.info.total_budget_money
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            submitBudget: async function() {
                let _self = this;
                let checkState = _self.checkAmortizeState('budget');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                console.log(_self.budgetState.text)
                if(_self.budgetState.text === undefined){
                    common.dealPromptCommon('未增加施工项，请先增加施工项再提交审核')
                    return
                }else {
                    common.dealConfrimCommon('确认提交审核？',async function() {
                        try {
                            let params = {};
                            params.amortize_id = _self.amortize_id;
                            params.amortize_name = _self.amortizeInfo.amortize_name;
                            let response = await _self.$http.post(apiUrl + 'sendBudgetTask' , params);
                            $('#budgetTable').bootstrapTable("refresh");
                        } catch (error) {
                            common.dealErrorCommon(_self,error)
                        }
                    });
                }
            },
            addSubscribe: function(event) {
                let _self = this;
                _self.workRow = {};
                let checkState = _self.checkAmortizeState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                let formB = $('#formB');
                console.log(formB.parsley())
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
                        // $('#subscribeTable').bootstrapTable('insertRow', params);
                        console.log(params,'params_add')
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
                let checkState = _self.checkAmortizeState();
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                if(_self.searchRow.length === 0){
                    return common.dealPromptCommon('申购表是空的，提交审核失败')
                }else{
                    common.dealConfrimCommon('确定提交审核？', async function () {
                        try {
                            let params = {};
                            params.amortize_id = _self.amortize_id;
                            params.amortizebudget_id  = _self.amortizebudget_id;
                            params.subscribeArr = _self.searchRow;
                            if(params.subscribeArr.length==0){

                                return common.dealPromptCommon('申购表是空的，提交审核失败')
                            }
                            let response = await _self.$http.post(apiUrl + 'sendSubscribeTask', params);
                            console.log(params,'params_submit')
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
                    params.amortize_id = _self.amortize_id;
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
                let checkState = _self.checkAmortizeState();
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
                        params.amortize_id = _self.$route.query.amortize_id;
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
                let checkState = _self.checkAmortizeState();
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
                        params.amortize_id = _self.amortize_id;
                        params.receiveDetailItem = _self.searchedRow;
                        let response = await _self.$http.post(apiUrl + 'addReceive', params);
                        $('#amortizeReceiveTable').bootstrapTable("refresh")
                        _self.searchedRow = []
                    }catch (error) {
                        common.dealErrorCommon(_self,error)
                    }
                });
            },
            submitConsume: function(){
                let _self = this;
                let checkState = _self.checkAmortizeState('consume');
                if(!checkState.state){
                    return common.dealPromptCommon(checkState.message);
                }
                common.dealConfrimCommon('确定提交耗用审核？', async function () {
                    try{
                        let params = {};
                        params.amortize_id = _self.amortize_id;
                        params.amortize_name = _self.amortizeInfo.amortize_name;
                        let response = await _self.$http.post(apiUrl + 'sendConumeTask', params)
                        $('#amortizeConsumeTable').bootstrapTable("refresh")
                    } catch (error) {
                        common.dealErrorCommon(_self,error)
                    }
                });
            },
            AddModalCost: function (event) {
                let _self = this;
                _self.workRow = {};
                let checkState = _self.checkAmortizeState('cost');
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
                        params.amortizecost_name = _self.workRow.amortizecost_name;
                        params.amortizecost_invoice_money = _self.workRow.amortizecost_invoice_money?_self.workRow.amortizecost_invoice_money:0;
                        params.amortizecost_noinvoice_money = _self.workRow.amortizecost_noinvoice_money?_self.workRow.amortizecost_noinvoice_money:0;
                        params.amortize_id = _self.$route.query.amortize_id;
                        let response = await _self.$http.post(apiUrl + 'addCost', params);
                        $('#amortizecostTable').bootstrapTable("refresh");
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
                let checkState = _self.checkAmortizeState('cost');
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
                            params.amortize_id = _self.amortize_id;
                            params.amortize_name = _self.amortizeInfo.amortize_name;
                            let response = await _self.$http.post(apiUrl + 'sendCostTask', params);
                            $('#amortizecostTable').bootstrapTable("refresh")
                        } catch(error){
                            common.dealErrorCommon(_self,error)
                        }
                    });
                }
            },
            submitClear: function(){
                let _self=this;
                let checkState = _self.checkAmortizeState('clear');
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
                                params.amortize_id = _self.amortize_id;
                                params.amortize_name = _self.amortizeInfo.amortize_name;
                                let response = await _self.$http.post(apiUrl + 'sendClearingTask', params);
                                $('#amortizeclearTable').bootstrapTable("refresh")
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
                let checkState = _self.checkAmortizeState();
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
                            params.amortize_id = _self.amortize_id;
                            params.amortize_name = _self.amortizeInfo.amortize_name;
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
                    window.location.href = '/static/baseDataTemplate/NCAAmortizeBudget.xlsx';
                }else if (type == 2){
                    window.location.href = '/static/baseDataTemplate/NCAAmortizeSubscribe.xlsx';
                }

            },
            importData: function(type) {
                let _self = this;
                this.dataType = type;
                if(this.dataType == 1){
                    let checkState = _self.checkAmortizeState();
                    checkState = _self.checkAmortizeState('subscribe');
                    if(!checkState.state){
                        return common.dealPromptCommon(checkState.message);
                    }
                    console.log(this.dataType,'this.dataType9')
                }else{
                    let checkState = _self.checkAmortizeState();
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
                    amortize_id:this.amortize_id,
                    amortizebudget_id:this.amortizebudget_id,
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
                let response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
                console.log(response,'response1')
                response = await _self.$http.post(apiUrl + 'getSumBudgetMoney', {amortize_id:_self.amortize_id});
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
        font-size: 14px;
    }
    .margin-left{
        margin-right: 2em;
    }
    .padding-left{
        padding-left: 2em;
    }
</style>
