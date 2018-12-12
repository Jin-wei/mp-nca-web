<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">产品规划审核</a></li>
        <li class="active">产品规划审核物料列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <span>评审项目：{{queryData.requireName}}</span>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">
                        产品规划审核物料列表
                    </h4>
                </div>

                <div class="panel-body auto-height">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="tabIndex = 0">
                            <a href="#tabDetail" data-toggle="tab">
                                <span class="visible-xs">物料详情</span>
                                <span class="hidden-xs">物料详情</span>
                            </a>
                        </li>
                        <li @click="tabIndex = 1">
                            <a href="#tabRelated" data-toggle="tab">
                                <span class="visible-xs">联产品</span>
                                <span class="hidden-xs">联产品</span>
                            </a>
                        </li>
                        <li @click="tabIndex = 2">
                            <a href="#tabRemain" data-toggle="tab">
                                <span class="visible-xs">边余料</span>
                                <span class="hidden-xs">边余料</span>
                            </a>
                        </li>
                        <li @click="tabIndex = 3">
                            <a href="#tabProcedure" data-toggle="tab">
                                <span class="visible-xs">工序管理</span>
                                <span class="hidden-xs">工序管理</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="tabDetail">
                            <table id="tableVerifyMateriel"></table>
                        </div>
                        <div class="tab-pane fade" id="tabRelated">
                            <table id="tableVerifyRelated1"></table>
                        </div>
                        <div class="tab-pane fade" id="tabRemain">
                            <table id="tableVerifyRelated2"></table>
                        </div>
                        <div class="tab-pane fade" id="tabProcedure">
                            <table id="tableVerifyProcedure"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAProductPlanControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {},
            queryData: {},
            searchedRow:[],
            searchText: null,
            tableVerify: []
        }
    },
    name: 'NCAPlanVerifyMaterielControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'initPlanVerify', {});
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async getProductMateriel() {
            try {
                let response = await this.$http.post(apiUrl + 'searchProductDetail', {materiel_id: this.queryData.materielId});
                this.pagePara.materielInfo = response.data.info.rows.map(item => {
                    return {
                        id: item.materiel_id,
                        text: item.materiel_code
                    }
                });
                $('#product_materiel').html('');
                common.initSelect2Editable($('#product_materiel'), this.pagePara.materielInfo);
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async initTablePlanVerifyMateriel(type){
            this.tableVerify[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchPlanMaterielFromOrderRequire',
                queryParams: (params) => {
                    params.product_id = this.queryData.productId;
                    params.require_id = this.queryData.requireId;
                    params.verify_type = type + 1;
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'prd_level', '产品层级', 'prdLevelInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'level_materiel_id', '上级投料', 'materielLevelInfo'),
                    common.BTRowFormatAlignRight('design_number', '设计投料数量'),
                    common.BTRowFormatAlignRight('loss_rate', '损耗率%'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_state_management', '状态分类', 'stateManagementInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_source', '来源分类', 'materielSource'),
                    common.BTRowFormatEdSelect2Disabled(this, 'workshop_id', '对应车间', 'departmentInfo'),
                ],
                idField: 'product_id',
                uniqueId: 'product_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass(this.tableVerify[type])
        },
        async initTablePlanVerifyRelated(type){
            const codeLabel = ['', '产品编码', '物料编码'];
            const nameLabel = ['', '产品名称', '物料名称'];
            this.tableVerify[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchPlanRelatedFromOrderRequire',
                queryParams: (params) => {
                    params.product_id = this.queryData.productId;
                    params.require_id = this.queryData.requireId;
                    params.verify_type = type + 1;
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', codeLabel[type]),
                    common.BTRowFormatAlignLeft('materiel_name', nameLabel[type]),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('prd_number', '数量'),
                    common.BTRowFormatEdSelect2Disabled(this, 'rlt_materiel_code', '对应物料编码', 'materielInfo')
                ],
                idField: 'product_id',
                uniqueId: 'product_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass(this.tableVerify[type])
        },
        async initTablePlanVerifyProcedure(type){
            this.tableVerify[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchPlanProcedureFromOrderRequire',
                queryParams: (params) => {
                    params.product_id = this.queryData.productId;
                    params.require_id = this.queryData.requireId;
                    params.verify_type = type + 1;
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('procedure_name', '工序名称'),
                    common.BTRowFormat('priority', '优先级'),
                    common.BTRowFormat('materiel_code', '对应物料编码'),
                ],
                idField: 'product_id',
                uniqueId: 'product_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass(this.tableVerify[type])
        }
    },
    async mounted() {
        this.queryData = this.$route.query;
        this.tableVerify[0] = $('#tableVerifyMateriel');
        this.tableVerify[1] = $('#tableVerifyRelated1');
        this.tableVerify[2] = $('#tableVerifyRelated2');
        this.tableVerify[3] = $('#tableVerifyProcedure');
        await this.getPagePara();
        await this.getProductMateriel();
        await this.initTablePlanVerifyMateriel(0);
        await this.initTablePlanVerifyRelated(1);
        await this.initTablePlanVerifyRelated(2);
        await this.initTablePlanVerifyProcedure(3);
        common.reSizeCall();
    },
}
</script>
<style scoped>
    .m_t{
        margin-top: 15px;
        margin-left: 10px;
        margin-right: 10px;
    }
</style>
