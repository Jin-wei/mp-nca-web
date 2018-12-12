<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">生产管理</a></li>
        <li class="active">产品规划评审</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">产品规划评审</h4>
                </div>
                <div class="form-inline panel-toolbar" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索产品编码、名称" v-model="searchText" style="width: 180px">
                        </div>
                        <div class="form-group">
                            <select class="form-control select2" id="state_select"></select>
                        </div>
                        <div class="form-group">
                            <select class="form-control select2" id="source_select"></select>
                        </div>
                        <div class="form-group">
                            <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                </div>

                <div class="panel-body auto-height">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="searchProductPlan(0)">
                            <a href="#tabTable" data-toggle="tab">
                                <span class="visible-xs">待审核</span>
                                <span class="hidden-xs">待审核</span>
                            </a>
                        </li>
                        <li @click="searchProductPlan(1)">
                            <a href="#tabTable" data-toggle="tab">
                                <span class="visible-xs">审核中</span>
                                <span class="hidden-xs">审核中</span>
                            </a>
                        </li>
                        <li @click="searchProductPlan(2)">
                            <a href="#tabTable" data-toggle="tab">
                                <span class="visible-xs">已审核</span>
                                <span class="hidden-xs">已审核</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="tabTable">
                            <table id="tableProduct"></table>
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
            searchedRow:[],
            searchText: null,
            tabIndex: 0,
            tableProduct: null
        }
    },
    name: 'NCAProductPlanVerifyControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'initProductPlan', {});
                this.pagePara = response.data.info;
                common.initSelect2Placeholder($('#state_select'), this.pagePara.stateManagementInfo, '状态分类');
                common.initSelect2Placeholder($('#source_select'), this.pagePara.materielSource, '来源分类');
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async searchProductPlan(index) {
            this.tabIndex = index;
            this.tableProduct.bootstrapTable("refresh")
        },
        queryConfirm() {
            this.tableProduct.bootstrapTable("refresh")
        },
        async initTableProduct(){
            window.tableEvents = {
                'click .delete_produce': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProductPlan', this.tableProduct, row, 'product_id', function(){})
                }
            };
            this.tableProduct.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductPlan',
                queryParams: (params) => {
                    params.valid_state = this.tabIndex;
                    params.materiel_state_management = common.getSelect2Val('state_select');
                    params.materiel_source = common.getSelect2Val('source_select');
                    params.search_text = this.searchText;
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
                    common.BTRowFormatWithFE('materiel_code', '产品编码', (value) => {
                        return `<a class="show_detail" style="cursor: pointer;">${value}</a>`
                    }, {
                        'click .show_detail': (e, value, row, index) => {
                            this.$router.push({
                                path: '/nca/productionmanage/NCAPlanVerifyListControl',
                                query: {
                                    productId: row.product_id,
                                    materielId: row.materiel_id
                                }
                            });
                        }
                    }),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('design_number', '设计投料数量'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_state_management', '状态分类', 'stateManagementInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_source', '来源分类', 'materielSource'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_amto', '制品分类', 'materielAmto'),
                    common.BTRowFormatEdSelect2Disabled(this, 'order_id', '订单号', 'orderInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'valid_state', '评审状态', 'validInfo'),
                    // common.actFormatter('act', () => {
                    //     return `<button class="btn btn-primary btn-xs m-r-5 delete_produce btn-info-delete">删除</button>`
                    // }, tableEvents)
                ],
                idField: 'product_id',
                uniqueId: 'product_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row);
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modifyProductPlan', row, 'product_id', this.tableProduct);
                }
            });
            common.changeTableClass(this.tableProduct)
        }
    },
    async mounted() {
        this.tableProduct = $('#tableProduct');
        await this.getPagePara();
        await this.initTableProduct();
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
