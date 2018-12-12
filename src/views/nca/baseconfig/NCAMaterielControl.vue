<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">运营数据管理</a></li>
            <li class="active">物料数据维护列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">物料数据列表</h4>
                    </div>
                    <!--<DomainChooseHeader :pagePara="pagePara" :apiName="apiName" v-show="selectState == 0"></DomainChooseHeader>-->
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索物料编码、名称、型号" style="width: 180px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button v-show="selectState == 0" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <ul class="nav nav-tabs">
                            <li class="active"><a href="#default-tab" @click="stateTypeSwitch(0)" data-toggle="tab">全部物料</a>
                            </li>
                            <li><a href="#default-tab" @click="stateTypeSwitch(1)" data-toggle="tab">待审核</a>
                            </li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(2)" data-toggle="tab">已审核</a>
                            </li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(3)" data-toggle="tab">未通过</a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="default-tab">
                                <table id="materieltable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">物料信息</h4>
                    </div>
                    <form @submit.prevent="addAct" id="formA">
                        <div class="modal-body row">
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>物料编号:</label>
                                <input class="form-control" v-model="workRow.review_materiel_code" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" :disabled="inputDisabled">
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>物料名称:</label>
                                <input class="form-control" v-model="workRow.review_materiel_name" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>来源分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_source" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>管理模式分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_manage" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>状态分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_state_management" data-parsley-required="true"> </select>
                            </div>
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label><span class="table-required">*</span>算料公式:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_formula" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>算料规格:</label>-->
                            <!--<input class="form-control" v-model="workRow.review_materiel_formatcount" maxlength="100" data-parsley-maxlength="100">-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>算料规格: (单位: mm)</label>-->
                            <!--<div style="width:295px;">-->
                            <!--<input v-model="workRow.review_materiel_x" style="width:89px;height:34px;" placeholder="长">-->
                            <!--<input v-model="workRow.review_materiel_y" style="width:89px;height:34px;" placeholder="宽">-->
                            <!--<input v-model="workRow.review_materiel_z" style="width:89px;height:34px;" placeholder="厚或高">-->
                            <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>规格单位:</label>-->
                            <!--<input class="form-control" v-model="workRow.review_materiel_formatunit" maxlength="100" data-parsley-maxlength="100">-->
                            <!--</div>-->
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>计算单位:</label>
                                <select class="form-control select2" multiple id="review_materiel_unit" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>物料分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_type" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>制品分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_amto" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>采购预算价:</label>
                                <input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                       class="form-control" v-model="workRow.review_materiel_cost" id="review_materiel_cost_input"
                                       maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>销售指导价:</label>
                                <input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                       class="form-control" v-model="workRow.review_materiel_sale" id="review_materiel_sale_input"
                                       maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <!--<div class="form-group col-xs-6 p_r">-->
                            <!--<label>税率:</label>-->
                            <!--<input type="text" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"-->
                            <!--oninput="if(value.length>7)value=value.slice(0,7)" placeholder="0" class="form-control" v-model="workRow.review_materiel_tax" maxlength="100" data-parsley-maxlength="100">-->
                            <!--<span class="p_a" style="font-size:25px;">%</span>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6 p_r">-->
                            <!--<label>损耗率:</label>-->
                            <!--<input type="text" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');" -->
                            <!--oninput="if(value.length>7)value=value.slice(0,7)" placeholder="0" class="form-control" id="review_materiel_loss" v-model="workRow.review_materiel_loss" maxlength="100" data-parsley-maxlength="100">-->
                            <!--<span class="p_a" style="font-size:25px;">%</span>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label><span class="table-required">*</span>计算单位转换:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_conversion" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>是否取整:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_intpart" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>发包价:</label>-->
                            <!--<input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');" -->
                            <!--class="form-control" v-model="workRow.review_materiel_award_cost" -->
                            <!--maxlength="100" data-parsley-maxlength="100" id="materiel_award_cost_input">-->
                            <!--</div>-->
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>采购类型:</label>
                                <select class="form-control select2" multiple id="review_materiel_procurement_type" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>规格型号:</label>
                                <input class="form-control" v-model="workRow.review_materiel_format" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>审核人:</label>-->
                            <!--<select class="form-control select2" multiple id="review_performer" data-parsley-required="true"></select>-->
                            <!--</div>-->
                            <div class="form-group col-xs-12">
                                <label>描述:</label>
                                <textarea rows="3" cols="20" class="form-control" v-model="workRow.review_materiel_describe" maxlength="200" data-parsley-maxlength="200"></textarea>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit"  id="submit-btn"  class="btn btn-primary btn-info">提交
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="Modify">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">物料信息</h4>
                    </div>
                    <form @submit.prevent="ModifyMaterAct" id="formB">
                        <div class="modal-body row">
                            <div class="form-group col-xs-6">
                                <label>物料编号:</label>
                                <input class="form-control" v-model="detailData.review_materiel_coden" id="materiel_coden" disabled="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>物料名称:</label>
                                <input class="form-control" v-model="detailData.review_materiel_namen" id="materiel_namen" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>来源分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_sourcen" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>管理模式分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_managen" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>状态分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_state_managementn" data-parsley-required="true"> </select>
                            </div>
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>算料公式:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_formulan" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>算料规格:</label>-->
                            <!--<input class="form-control" v-model="detailData.review_materiel_formatcountn" id="materiel_formatcountn" maxlength="100" data-parsley-maxlength="100">-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>算料规格: (单位: mm)</label>-->
                            <!--<div style="width:295px;">-->
                            <!--<input v-model="workRow.review_materiel_xn" id="materiel_xn" style="width:89px;height:34px;" placeholder="长">-->
                            <!--<input v-model="workRow.review_materiel_yn" id="materiel_yn" style="width:89px;height:34px;" placeholder="宽">-->
                            <!--<input v-model="workRow.review_materiel_zn" id="materiel_zn" style="width:89px;height:34px;" placeholder="厚或高">-->
                            <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>规格单位:</label>-->
                            <!--<input class="form-control" v-model="detailData.review_materiel_formatunitn" id="materiel_formatunitn" maxlength="100" data-parsley-maxlength="100">-->
                            <!--</div>-->
                            <div class="form-group col-xs-6">
                                <label>计量单位:</label>
                                <select class="form-control select2" multiple id="review_materiel_unitn" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>物料分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_typen" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>制品分类:</label>
                                <select class="form-control select2" multiple id="review_materiel_amton" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>采购预算价:</label>
                                <input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                       class="form-control" v-model="detailData.review_materiel_costn" id="materiel_costn" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>销售指导价:</label>
                                <input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                       class="form-control" v-model="detailData.review_materiel_salen" id="materiel_salen" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <!--<div class="form-group col-xs-6 p_r">-->
                            <!--<label>税率:</label>-->
                            <!--<input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"-->
                            <!--class="form-control" v-model="workRow.review_materiel_taxn" id="materiel_taxn" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">-->
                            <!--<span class="p_a" style="font-size:25px;">%</span>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6 p_r">-->
                            <!--<label>损耗率:</label>-->
                            <!--<input onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"-->
                            <!--class="form-control" id="materiel_lossn" v-model="workRow.review_materiel_lossn" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">-->
                            <!--<span class="p_a" style="font-size:25px;">%</span>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>计算单位转换:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_conversionn" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-6">-->
                            <!--<label>是否取整:</label>-->
                            <!--<select class="form-control select2" multiple id="review_materiel_intpartn" data-parsley-required="true"> </select>-->
                            <!--</div>-->
                            <div class="form-group col-xs-6">
                                <label><span class="table-required">*</span>采购类型:</label>
                                <select class="form-control select2" multiple id="review_materiel_procurement_typen" data-parsley-required="true"> </select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>规格型号:</label>
                                <input class="form-control" v-model="detailData.review_materiel_formatn" id="materiel_formatn" maxlength="100" data-parsley-maxlength="100">
                            </div>
                            <div class="form-group col-xs-12">
                                <label>描述:</label>
                                <textarea rows="3" cols="20" class="form-control" v-model="detailData.review_materiel_describen" id="materiel_describen"  maxlength="200" data-parsley-maxlength="200"></textarea>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">提交</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCAMaterielControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'

    export default {
        data: function () {
            return {
                userinfo: common.getStoreData('userinfo'),
                apiName: common.getApiName(apiUrl),
                workRow: {},
                oldRow: '',
                pagePara: {},
                selectState: 0,//0全部物料 1待审核 2已审核 3未通过
                detailData:{},
                isEdit:false,
                inputDisabled:false
            }
        },
        name: 'NCAMaterielControl',
        components: {
            DomainChooseHeader
        },
        mounted: function () {
            let _self = this;

            async function initMaterielData() {
                await _self.initData();
                _self.initTable();
            }

            $(function () {
                initMaterielData();
                common.reSizeCall();
            })
        },
        methods: {
            initData: async function () {
                let _self = this;

                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
                    console.log(_self.pagePara)
                    $('#domain_select').on('change', function(evt) {
                        _self.queryConfirm()
                    });
                    common.initSelect2($('#review_materiel_source'), _self.pagePara.materielSource);
                    // common.initSelect2($('#review_materiel_formulan'), _self.pagePara.formulaInfo)
                    common.initSelect2($('#review_materiel_manage'), _self.pagePara.materielManage);
                    common.initSelect2($('#review_materiel_unit'), _self.pagePara.unitInfo);
                    common.initSelect2($('#review_materiel_type'), _self.pagePara.materielType);
                    common.initSelect2($('#review_materiel_amton'), _self.pagePara.materielAmto)
                    common.initSelect2($('#review_materiel_state_management'), _self.pagePara.stateManagement);
                    common.initSelect2($('#review_materiel_procurement_type'), _self.pagePara.procurementType);
                    common.initSelect2($('#review_materiel_sourcen'), _self.pagePara.materielSource);
                    // common.initSelect2($('#review_materiel_formula'), _self.pagePara.formulaInfo)
                    common.initSelect2($('#review_materiel_conversionn'), _self.pagePara.materielConversion);
                    common.initSelect2($('#review_materiel_intpartn'), _self.pagePara.materielIntpart);
                    common.initSelect2($('#review_materiel_managen'), _self.pagePara.materielManage);
                    common.initSelect2($('#review_materiel_state_managementn'), _self.pagePara.stateManagement);
                    common.initSelect2($('#review_materiel_procurement_typen'), _self.pagePara.procurementType);
                    common.initSelect2($('#review_materiel_unitn'), _self.pagePara.unitInfo);
                    common.initSelect2($('#review_materiel_typen'), _self.pagePara.materielType);
                    common.initSelect2($('#review_performern'), _self.pagePara.staffInfo);
                    common.initSelect2($('#review_materiel_conversion'), _self.pagePara.materielConversion);
                    common.initSelect2($('#review_materiel_intpart'), _self.pagePara.materielIntpart);
                    common.initSelect2($('#review_materiel_amto'), _self.pagePara.materielAmto)
//                common.initSelect2($('#review_performer'), _self.pagePara.staffInfo);
                    $('#review_materiel_amto').on('select2:select', function(evt) {
                        let mValue = $('#review_materiel_amto').val()[0]
                        if (mValue === '2') {
                            _self.workRow.review_materiel_sale = 0
                            _self.workRow.review_materiel_cost = 0
                            $('#review_materiel_sale_input').attr("disabled", true)
                            $('#review_materiel_cost_input').attr("disabled", true)
                        } else {
                            $('#review_materiel_sale_input').attr("disabled", false)
                            $('#review_materiel_cost_input').attr("disabled", false)
                        }
                    })
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            },
            initTable: function (){
                let $materieltable = $('#materieltable');
                let _self = this;
                window.tableEvents = {
                    'click .review_delete': function(e, value, row, index) {
                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $materieltable, row, 'review_materiel_id', function() {})
                    },
                    'click .detail_edit': function(e, value, row, index) {
                        _self.isEdit=true;
                        _self.inputDisabled=true;
                        _self.oldRow = $.extend(true, {}, row)
                        _self.workRow = $.extend(true, {}, row)
                        _self.workRow.review_materiel_cost = row.review_materiel_cost.substr(1,row.review_materiel_cost.length);
                        _self.workRow.review_materiel_sale = row.review_materiel_sale.substr(1,row.review_materiel_sale.length);
                        // _self.workRow.review_materiel_tax = row.review_materiel_tax.substr(0,row.review_materiel_tax.length-1);
                        // _self.workRow.review_materiel_loss = row.review_materiel_loss.substr(0,row.review_materiel_loss.length-1);

                        $('#review_materiel_source').val(_self.workRow.review_materiel_source).trigger('change');
                        // $('#review_materiel_formula').val(_self.workRow.review_materiel_formula).trigger('change')
                        // $('#review_materiel_conversion').val(_self.workRow.review_materiel_conversion).trigger('change')
                        // $('#review_materiel_intpart').val(_self.workRow.review_materiel_intpart).trigger('change')
                        $('#review_materiel_manage').val(_self.workRow.review_materiel_manage).trigger('change');
                        $('#review_materiel_state_management').val(_self.workRow.review_materiel_state_management).trigger('change');
                        $('#review_materiel_procurement_type').val(_self.workRow.review_materiel_procurement_type).trigger('change');
                        $('#review_materiel_unit').val(_self.workRow.review_materiel_unit).trigger('change');
                        $('#review_materiel_type').val(_self.workRow.review_materiel_type).trigger('change');
                        $('#review_materiel_amto').val(_self.workRow.review_materiel_amto).trigger('change')
                        $('#submit-btn').show();
                        $('#AddModal').modal('show')
                    },
                    'click .look_detail': function(e, value, row, index) {
                        _self.isEdit=true;
                        _self.inputDisabled=true;
                        _self.oldRow = $.extend(true, {}, row)
                        _self.workRow = $.extend(true, {}, row)
                        _self.workRow.review_materiel_cost = row.review_materiel_cost.substr(1,row.review_materiel_cost.length);
                        _self.workRow.review_materiel_sale = row.review_materiel_sale.substr(1,row.review_materiel_sale.length);
                        // _self.workRow.review_materiel_tax = row.review_materiel_tax.substr(0,row.review_materiel_tax.length-1);
                        // _self.workRow.review_materiel_loss = row.review_materiel_loss.substr(0,row.review_materiel_loss.length-1);

                        $('#review_materiel_source').val(_self.workRow.review_materiel_source).trigger('change');
                        // $('#review_materiel_formula').val(_self.workRow.review_materiel_formula).trigger('change')
                        // $('#review_materiel_conversion').val(_self.workRow.review_materiel_conversion).trigger('change')
                        // $('#review_materiel_intpart').val(_self.workRow.review_materiel_intpart).trigger('change')
                        $('#review_materiel_manage').val(_self.workRow.review_materiel_manage).trigger('change');
                        $('#review_materiel_state_management').val(_self.workRow.review_materiel_state_management).trigger('change');
                        $('#review_materiel_procurement_type').val(_self.workRow.review_materiel_procurement_type).trigger('change');
                        $('#review_materiel_unit').val(_self.workRow.review_materiel_unit).trigger('change');
                        $('#review_materiel_type').val(_self.workRow.review_materiel_type).trigger('change');
                        $('#review_materiel_amto').val(_self.workRow.review_materiel_amto).trigger('change')

                        $('#submit-btn').hide();
                        $('#AddModal').modal('show')
                    },
                    "click .edit_detail" :function (e, value, row, index){
                        _self.isEdit=true;
                        _self.detailData=row;

                        if(row.domain_id == common.getStoreData('userinfo').domain_id){
                            _self.detailData.review_materiel_coden = row.materiel_code;
                            $('#materiel_coden').val(row.materiel_code).trigger('change');
                            _self.detailData.review_materiel_namen = row.materiel_name;
                            $('#materiel_namen').val(_self.detailData.review_materiel_namen).trigger('change');
                            _self.detailData.review_materiel_formatn = row.materiel_format;
                            $('#materiel_formatn').val(_self.detailData.review_materiel_formatn).trigger('change');
                            // _self.detailData.review_materiel_formatcountn = row.materiel_formatcount;
                            // $('#materiel_formatcountn').val(_self.detailData.review_materiel_formatcountn).trigger('change')
                            // _self.detailData.review_materiel_formatunitn = row.materiel_formatunit;
                            // $('#materiel_formatunitn').val(_self.detailData.review_materiel_formatunitn).trigger('change')
                            // _self.detailData.review_materiel_formatunitn = row.materiel_formatunit;
                            // $('#materiel_formatunitn').val(_self.detailData.review_materiel_formatunitn).trigger('change')
                            _self.detailData.review_materiel_costn = row.materiel_cost.substr(1,row.materiel_cost.length);
                            $('#materiel_costn').val(_self.detailData.review_materiel_costn).trigger('change');
                            _self.detailData.review_materiel_salen = row.materiel_sale.substr(1,row.materiel_sale.length);
                            $('#materiel_salen').val(_self.detailData.review_materiel_salen).trigger('change');
                            $('#review_materiel_amton').on('select2:select', function(evt) {
                                let mValue = $('#review_materiel_amton').val()[0]
                                if (mValue === '2') {
                                    $('#materiel_salen').val('0');
                                    $('#materiel_costn').val('0');
                                    _self.detailData.review_materiel_salen = 0
                                    _self.detailData.review_materiel_costn = 0
                                    $('#materiel_salen').attr("disabled", true)
                                    $('#materiel_costn').attr("disabled", true)
                                } else {
                                    $('#materiel_salen').attr("disabled", false)
                                    $('#materiel_costn').attr("disabled", false)
                                }
                            })
                            // _self.detailData.review_materiel_taxn = row.materiel_tax.substr(0,row.materiel_tax.length-1);
                            // $('#materiel_taxn').val(_self.detailData.review_materiel_taxn).trigger('change')
                            // _self.detailData.review_materiel_lossn = row.materiel_loss.substr(0,row.materiel_loss.length-1);
                            // $('#materiel_lossn').val(_self.detailData.review_materiel_lossn).trigger('change')
                            _self.detailData.review_materiel_describen = row.materiel_describe;
                            $('#materiel_describen').val(_self.detailData.review_materiel_describen).trigger('change');

                            // _self.detailData.review_materiel_xn = row.materiel_x;
                            // $('#materiel_xn').val(_self.detailData.review_materiel_xn).trigger('change')
                            // _self.detailData.review_materiel_yn = row.materiel_y;
                            // $('#materiel_yn').val(_self.detailData.review_materiel_yn).trigger('change')
                            // _self.detailData.review_materiel_zn = row.materiel_z;
                            // $('#materiel_zn').val(_self.detailData.review_materiel_zn).trigger('change')

                            _self.detailData.materiel_sourcen = row.materiel_source;
                            // _self.detailData.materiel_formulan = row.materiel_formula;
                            _self.detailData.materiel_conversionn = row.materiel_conversion;
                            _self.detailData.materiel_intpartn = row.materiel_intpart;
                            _self.detailData.materiel_managen = row.materiel_manage;
                            _self.detailData.materiel_unitn = row.materiel_unit;
                            _self.detailData.materiel_typen = row.materiel_type;
                            _self.detailData.materiel_amton = row.materiel_amto;
                            _self.detailData.materiel_state_managementn = row.materiel_state_management;
                            _self.detailData.materiel_procurement_typen = row.materiel_procurement_type;
                            $('#review_materiel_sourcen').val(_self.detailData.materiel_sourcen).trigger('change');
                            // $('#review_materiel_formulan').val(_self.detailData.materiel_formulan).trigger('change')
                            $('#review_materiel_conversionn').val(_self.detailData.materiel_conversionn).trigger('change');
                            $('#review_materiel_intpartn').val(_self.detailData.materiel_intpartn).trigger('change');
                            $('#review_materiel_managen').val(_self.detailData.materiel_managen).trigger('change');
                            $('#review_materiel_state_managementn').val(_self.detailData.materiel_state_managementn).trigger('change');
                            $('#review_materiel_procurement_typen').val(_self.detailData.materiel_procurement_typen).trigger('change');
                            $('#review_materiel_unitn').val(_self.detailData.materiel_unitn).trigger('change');
                            $('#review_materiel_typen').val(_self.detailData.materiel_typen).trigger('change');
                            $('#review_materiel_amton').val(_self.detailData.materiel_amton).trigger('change');
                            $('#submit-btn').show();
                            $('#Modify').modal('show');
                        }else{
                            common.dealPromptCommon('该物料不属于本机构，不可编辑')
                        }
                    }
                }

                function queryParams(params) {
                    let domain_id = common.getSelect2Val('domain_select')
                    if (domain_id) {
                        params.domain_id = domain_id
                    }
                    params.search_text = $('#search_text').val();
                    if (_self.selectState==0) {//全部物料

                    } else if (_self.selectState==1){//待审核
                        params.review_state = 1;
                    } else if (_self.selectState==2) {//已审核
                        params.review_state = 2;
                    } else if (_self.selectState==3) {//未通过
                        params.review_state = 3;
                    }
                    return JSON.stringify(params)
                }

                function actFormatter(value, row) {
                    //全部物料
                    if (_self.selectState==0){
                        if(row.domain_id == common.getStoreData('userinfo').domain_id){
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑</a>'
                            ].join('')
                        } else {
                            return [].join('')
                        }
                    }
                    //待审核 已经审核
                    if (_self.selectState==1 || _self.selectState==2){
                        if(row.domain_id == common.getStoreData('userinfo').domain_id){
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 look_detail">查看</a>'
                            ].join('')
                        } else {
                            return [].join('')
                        }
                    }
                    //未通过
                    else if ((row.review_state == '3') && _self.selectState==3){
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 review_delete">删除</a>' +
                            '<a class="btn btn-info btn-xs m-r-5 detail_edit">编辑</a>'
                        ].join('')
                    }
                }

                let columns;
                let url;
                if(_self.selectState==0){//全部物料
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatWithFormatterAlignLeft('materiel_name', '物料名称',common.remarkFormatter),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '计算单位', 'unitInfo'),
                        // common.BTRowFormat('materiel_formatunit', '规格单位'),
                        //common.BTRowFormatAlignRight('materiel_cost', '采购预算价'),
                        //common.BTRowFormatAlignRight('materiel_sale', '销售指导价'),
                        // common.BTRowFormat('materiel_award_cost', '发包价'),
                        // common.BTRowFormat('materiel_x', '长'),
                        // common.BTRowFormat('materiel_y', '宽'),
                        // common.BTRowFormat('materiel_z', '厚或高'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'materiel_formula', '算料公式', 'formulaInfo'),
                        //common.BTRowFormat('materiel_formatcount', '算料规格'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '来源分类', 'materielSource'),
                        //common.BTRowFormatEdSelect2Disabled(_self, 'materiel_manage', '管理模式分类', 'materielManage'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '物料分类', 'materielType'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '制品分类', 'materielAmto'),
                        // common.BTRowFormat('materiel_tax', '税率'),
                        // common.BTRowFormat('materiel_loss', '损耗率'),
                        //common.BTRowFormat('materiel_describe', '描述'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_state_management', '状态分类', 'stateManagement'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_procurement_type', '采购类型', 'procurementType'),
                        common.BTRowFormatEdSelect2(_self, 'state', '状态', 'statusInfo'),
                        // common.BTRowFormat('domain_name', '所属机构'),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]

                    url =  apiUrl + 'search';
                }else if(_self.selectState==1){//待审核
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('review_materiel_code', '物料编码'),
                        common.BTRowFormatWithFormatterAlignLeft('review_materiel_name', '物料名称',common.remarkFormatter),
                        common.BTRowFormat('review_materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_unit', '计量单位', 'unitInfo'),
                        // common.BTRowFormat('review_materiel_formatunit', '规格单位'),
                        //common.BTRowFormatAlignRight('review_materiel_cost', '采购预算价'),
                        //common.BTRowFormatAlignRight('review_materiel_sale', '销售指导价'),
                        // common.BTRowFormat('review_materiel_award_cost', '发包价'),
                        // common.BTRowFormat('materiel_x', '长'),
                        // common.BTRowFormat('materiel_y', '宽'),
                        // common.BTRowFormat('materiel_z', '厚或高'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_formula', '算料公式', 'formulaInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_source', '来源分类', 'materielSource'),
                        //common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_manage', '管理模式分类', 'materielManage'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_type', '物料分类', 'materielType'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_amto', '制品分类', 'materielAmto'),
                        // common.BTRowFormat('review_materiel_tax', '税率'),
                        // common.BTRowFormat('review_materiel_loss', '损耗率'),
                        //common.BTRowFormat('review_materiel_describe', '描述'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_state_management', '状态分类', 'stateManagement'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_procurement_type', '采购类型', 'procurementType'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_state', '状态', 'reviewInfo'),
//                    common.BTRowFormatEdSelect2Disabled(_self, 'review_performer', '审核人', 'staffInfo'),
                        common.BTRowFormatWithFormatterAlignLeft('review_performer', '审核人',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('review_remark', '任务说明',common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url =  apiUrl + 'search_r';
                }else if(_self.selectState==2){//已审核
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('review_materiel_code', '物料编码'),
                        common.BTRowFormatWithFormatterAlignLeft('review_materiel_name', '物料名称',common.remarkFormatter),
                        common.BTRowFormat('review_materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_unit', '计量单位', 'unitInfo'),
                        // common.BTRowFormat('review_materiel_formatunit', '规格单位'),
                        //common.BTRowFormatAlignRight('review_materiel_cost', '采购预算价'),
                        //common.BTRowFormatAlignRight('review_materiel_sale', '销售指导价'),
                        // common.BTRowFormat('review_materiel_award_cost', '发包价'),
                        // common.BTRowFormat('materiel_x', '长'),
                        // common.BTRowFormat('materiel_y', '宽'),
                        // common.BTRowFormat('materiel_z', '厚或高'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_formula', '算料公式', 'formulaInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_source', '来源分类', 'materielSource'),
                        //common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_manage', '管理模式分类', 'materielManage'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_type', '物料分类', 'materielType'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_amto', '制品分类', 'materielAmto'),
                        // common.BTRowFormat('review_materiel_tax', '税率'),
                        // common.BTRowFormat('review_materiel_loss', '损耗率'),
                        //common.BTRowFormat('review_materiel_describe', '描述'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_state_management', '状态分类', 'stateManagement'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_procurement_type', '采购类型', 'procurementType'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_state', '状态', 'reviewInfo'),
//                    common.BTRowFormatEdSelect2Disabled(_self, 'review_performer', '审核人', 'staffInfo'),
                        common.BTRowFormatWithFormatterAlignLeft('review_performer', '审核人',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('review_remark', '任务说明',common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url =  apiUrl + 'search_r';
                }else if(_self.selectState==3){//未通过
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('review_materiel_code', '物料编码'),
                        common.BTRowFormatWithFormatterAlignLeft('review_materiel_name', '物料名称',common.remarkFormatter),
                        common.BTRowFormat('review_materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_unit', '计量单位', 'unitInfo'),
                        // common.BTRowFormat('review_materiel_formatunit', '规格单位'),
                        /*common.BTRowFormatAlignRight('review_materiel_cost', '采购预算价'),
                         common.BTRowFormatAlignRight('review_materiel_sale', '销售指导价'),*/
                        // common.BTRowFormat('review_materiel_award_cost', '发包价'),
                        // common.BTRowFormat('materiel_x', '长'),
                        // common.BTRowFormat('materiel_y', '宽'),
                        // common.BTRowFormat('materiel_z', '厚或高'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_formula', '算料公式', 'formulaInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_source', '来源分类', 'materielSource'),
                        /*common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_manage', '管理模式分类', 'materielManage'),*/
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_type', '物料分类', 'materielType'),
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_amto', '制品分类', 'materielAmto'),
                        // common.BTRowFormat('review_materiel_tax', '税率'),
                        // common.BTRowFormat('review_materiel_loss', '损耗率'),
                        /*common.BTRowFormat('review_materiel_describe', '描述'),*/
                        // common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_state_management', '状态分类', 'stateManagement'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_materiel_procurement_type', '采购类型', 'procurementType'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'review_state', '状态', 'reviewInfo'),
//                    common.BTRowFormatEdSelect2Disabled(_self, 'review_performer', '审核人', 'staffInfo'),
                        common.BTRowFormatWithFormatterAlignLeft('review_performer', '审核人',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('review_remark', '任务说明',common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                    url =  apiUrl + 'search_r';
                }

                $materieltable.bootstrapTable('destroy');
                $materieltable.bootstrapTable({
                    method: 'POST',
                    url: url,
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    undefinedText:'',
                    onPostBody: function () {
//                        common.DynamicEditableByDomain(_self, $('#materieltable'))
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })

                        $('[data-name="state"]').each(function () {
                            let actrow = $('#materieltable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                            if(actrow.domain_id != common.getStoreData('userinfo').domain_id){
                                $(this).editable({
                                    type: 'select2',
                                    emptytext: '无',
                                    source: _self.pagePara['statusInfo'],
                                    disabled: true,
                                    display: function(value) {
                                        let showText = ''
                                        $(_self.pagePara['statusInfo']).each(function() {
                                            if (this.id == actrow.state) {
                                                if (this.name) {
                                                    showText = this.name
                                                } else {
                                                    showText = this.text
                                                }
                                                return false
                                            }
                                        })
                                        $(this).html(showText)
                                    }
                                })
                            }
                        })
                    },
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_table', row, 'materiel_id', $materieltable)
                    }
                })
                common.changeTableClass($materieltable)
            },
            addM: function (event) {
                let _self = this;
                _self.isEdit=false;
                _self.inputDisabled=false;
                _self.workRow = {}
                if(_self.selectState==0){
                    $('#review_materiel_source').val(null).trigger('change')
                    // $('#review_materiel_formula').val(null).trigger('change')
                    $('#review_materiel_conversion').val(null).trigger('change');
                    $('#review_materiel_intpart').val("1").trigger('change');
                    $('#review_materiel_manage').val(null).trigger('change');
                    $('#review_materiel_state_management').val(null).trigger('change');
                    $('#review_materiel_procurement_type').val(null).trigger('change');
                    $('#review_materiel_unit').val(null).trigger('change');
                    $('#review_materiel_type').val(null).trigger('change');
                    //$('#review_materiel_batch').val(null).trigger('change')
                    //$('#review_materiel_procedure').val(null).trigger('change')
                    $('#review_materiel_amto').val(null).trigger('change')
//                $('#review_performer').val(null).trigger('change')
                    $('#formA').parsley().reset()
                    $('#AddModal').modal('show')
                }
            },
            addAct:function () {
                let _self=this;
                if(_self.isEdit){
                    if (_self.selectState==0) {
                        _self.ModifyMaterAct();
                    } else {
                        _self.modifyAct();
                    }
                }else {
                    _self.addMaterielAct();
                }
            },
            modifyAct: function (event) {
                let _self = this;
                try {
                    _self.workRow.review_materiel_source = common.getSelect2Val('review_materiel_source');
                    // _self.workRow.review_materiel_formula = common.getSelect2Val('review_materiel_formula')
                    // _self.workRow.review_materiel_conversion = common.getSelect2Val('review_materiel_conversion')
                    // _self.workRow.review_materiel_intpart = common.getSelect2Val('review_materiel_intpart')
                    _self.workRow.review_materiel_manage = common.getSelect2Val('review_materiel_manage');
                    _self.workRow.review_materiel_state_management = common.getSelect2Val('review_materiel_state_management');
                    _self.workRow.review_materiel_procurement_type = common.getSelect2Val('review_materiel_procurement_type');
                    _self.workRow.review_materiel_unit = common.getSelect2Val('review_materiel_unit');
                    _self.workRow.review_materiel_type = common.getSelect2Val('review_materiel_type');
                    _self.workRow.review_materiel_amto = common.getSelect2Val('review_materiel_amto')
                    let response = _self.$http.post(apiUrl + 'modify_review', _self.workRow);
                    $('#AddModal').modal('hide');
                    window.setTimeout(function () {
                        $('#materieltable').bootstrapTable("refresh");
                    },500);
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            addMaterielAct: function () {
                let _self = this;
                try {
                    if ($('#formA').parsley().isValid()) {
                        _self.workRow.review_materiel_source = common.getSelect2Val('review_materiel_source')
                        // _self.workRow.review_materiel_formula = common.getSelect2Val('review_materiel_formula')
                        _self.workRow.review_materiel_conversion = common.getSelect2Val('review_materiel_conversion')
                        _self.workRow.review_materiel_intpart = common.getSelect2Val('review_materiel_intpart')
                        _self.workRow.review_materiel_manage = common.getSelect2Val('review_materiel_manage')
                        _self.workRow.review_materiel_state_management = common.getSelect2Val('review_materiel_state_management')
                        _self.workRow.review_materiel_procurement_type = common.getSelect2Val('review_materiel_procurement_type');
                        _self.workRow.review_materiel_unit = common.getSelect2Val('review_materiel_unit')
                        _self.workRow.review_materiel_type = common.getSelect2Val('review_materiel_type')
                        _self.workRow.review_materiel_amto = common.getSelect2Val('review_materiel_amto')
                        // _self.workRow.review_materiel_loss= (_self.workRow.review_materiel_loss)?_self.workRow.review_materiel_loss:0;
                        // _self.workRow.review_materiel_tax= (_self.workRow.review_materiel_tax)?_self.workRow.review_materiel_tax:0;
                        _self.workRow.review_materiel_cost= (_self.workRow.review_materiel_cost)?_self.workRow.review_materiel_cost:0;
                        _self.workRow.review_materiel_sale= (_self.workRow.review_materiel_sale)?_self.workRow.review_materiel_sale:0;

                        _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                            let retData = response.data.info;
                            $('#materieltable').bootstrapTable("refresh");
                            common.dealSuccessCommon('保存成功');
                            $('#AddModal').modal('hide');
                            console.log('add success');
                            $('#formA').parsley().reset();
                            _self.workRow = {};
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        });
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            queryConfirm :function () {
                let _self = this;
                $('#materieltable').bootstrapTable("refresh", _self.queryParams);
            },
            stateTypeSwitch: function (value) {
                let _self = this
                $('#search_text').val('');
                _self.selectState = value;
                _self.initTable();
            },
            ModifyMaterAct: function(event) {
                let _self = this;
                try {
                    _self.detailData.review_materiel_source = common.getSelect2Val('review_materiel_sourcen')
                    // _self.detailData.review_materiel_formula = common.getSelect2Val('review_materiel_formulan')
                    _self.detailData.review_materiel_conversion = common.getSelect2Val('review_materiel_conversionn')
                    _self.detailData.review_materiel_intpart = common.getSelect2Val('review_materiel_intpartn')
                    _self.detailData.review_materiel_manage = common.getSelect2Val('review_materiel_managen')
                    _self.detailData.review_materiel_state_management = common.getSelect2Val('review_materiel_state_managementn')
                    _self.detailData.review_materiel_procurement_type = common.getSelect2Val('review_materiel_procurement_typen');
                    _self.detailData.review_materiel_unit = common.getSelect2Val('review_materiel_unitn')
                    _self.detailData.review_materiel_type = common.getSelect2Val('review_materiel_typen')
                    _self.detailData.review_materiel_amto = common.getSelect2Val('review_materiel_amton')
                    console.log('params',_self.detailData)
                    let response = _self.$http.post(apiUrl + 'modify', _self.detailData)
                    $('#Modify').modal('hide');
                    window.setTimeout(function () {
                        $('#materieltable').bootstrapTable("refresh")
                    },500);
                    _self.detailData={}
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
</script>
<style scoped>
</style>

