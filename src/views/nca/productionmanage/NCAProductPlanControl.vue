<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">生产管理</a></li>
        <li class="active">产品规划列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">产品规划列表</h4>
                </div>
                <div class="form-inline panel-toolbar" role="form">
                    <div class="form-group ">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索产品编码、名称" v-model="searchText" style="width: 180px">
                        </div>
                        <div class="form-group">
                            <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                    <div class="form-group pull-right">
                        <button id="addProductPlan" class="btn btn-info" v-on:click="addProductPlan">增加
                        </button>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="margin-top: 0px">
                    <table id="tableProduct"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="OrderListModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增产品</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-10">
                        <input class="form-control" id="orderInfoInput" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-2">
                        <button type="button" class="btn btn-info" v-on:click="saveProduct">保存</button>
                    </div>
                    <table id="tableOrderList"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">添加产品规划</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-10">
                        <input class="form-control" v-model="searchData" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-2">
                        <button type="button" class="btn btn-info" v-on:click="saveProduct">保存</button>
                    </div>
                    <table id="tableMateriel"></table>
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
            searchData: null,
            tableProduct: null,
            tableOrderList: null,
            tableMateriel: null
        }
    },
    name: 'NCAProductPlanControl',
    methods: {
        async getPagePara(event) {
            try {
                let response = await this.$http.post(apiUrl + 'initProductPlan', {});
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },

        async addProductPlan() {
            await this.initTableMateriel();
            $('#AddMaterielModal').modal('show');
            this.searchedRow = [];
            $("input[type=checkbox]").each(function() {
                $(this).prop("checked", false);
            })
        },
        async saveProduct() {
            let params={
                searchedRow: this.searchedRow
            };
            let response = await this.$http.post(apiUrl + 'addProductPlan', params);
            this.tableProduct.bootstrapTable("refresh");
            $('#AddMaterielModal').modal('hide');
        },
        queryConfirm(event) {
            this.tableProduct.bootstrapTable("refresh")
        },
        searchMate(event) {
            this.searchedRow = [];
            this.tableMateriel.bootstrapTable("refresh")
        },
        async initTableProduct(event){
            window.tableEvents = {
                'click .delete_produce': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProductPlan', this.tableProduct, row, 'product_id', function(){})
                }
            };
            this.tableProduct.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductPlan',
                queryParams: (params) => {
                    let domain_id = common.getSelect2Val('domain_select');
                    if (domain_id) {
                        params.domain_id = domain_id
                    }
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
                                path: '/nca/productionmanage/NCAProductPlanDetailControl',
                                query: {
                                    productId: row.product_id,
                                    materielId: row.materiel_id,
                                    materielCode: row.materiel_code,
                                    materielName: row.materiel_name,
                                    validState: row.valid_state
                                }
                            });
                        }
                    }),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEditableRight('design_number', '设计投料数量'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_state_management', '状态分类', 'stateManagementInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_source', '来源分类', 'materielSource'),
                    common.BTRowFormatEdSelect2(this, 'order_id', '订单号', 'orderInfo'),
                    common.BTRowFormatEdSelect2(this, 'workshop_id', '对应车间', 'departmentInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'valid_state', '评审状态', 'validInfo'),
                    common.actFormatter('act', (value, row) => {
                        if (row.valid_state > 0) {
                            return `<button class="btn btn-info btn-xs m-r-5 delete_produce" disabled>删除</button>`
                        } else {
                            return `<button class="btn btn-info btn-xs m-r-5 delete_produce">删除</button>`
                        }
                    }, tableEvents)
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
                onPostBody: () => {
                    const localTable = this.tableProduct;
                    $("[data-uniqueid]").each(function() {
                        let actrow = localTable.bootstrapTable('getRowByUniqueId', this.getAttribute('data-uniqueid'))
                        console.log('actrow:', actrow);
                        if (actrow) {
                            if (actrow.valid_state > 0) {
                                $(this).find('[data-name]').each(function() {
                                    $(this).attr("data-disabled", true)
                                })
                            }
                        }
                    })
                },
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row);
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modifyProductPlan', row, 'product_id', this.tableProduct);
                }
            });
            common.changeTableClass(this.tableProduct)
        },

        async initTableMateriel(){
            this.tableMateriel.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMateriel',
                queryParams: (params) => {
                    params.searchData = this.searchData;
                    params.tableName = 'product';
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                // height: common.getTableHeight(),
                columns: [
                    {
                        field: 'state',
                        checkbox: true
                    },
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    // common.BTRowFormat('materiel_cost', '采购价'),
                    // common.BTRowFormat('materiel_sale', '销售价'),
                    // common.BTRowFormatEdSelect2Disabled(this, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormat('domain_name', '所属机构')
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                clickToSelect: true,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 25,50,100],
                locale: 'zh-CN',
                onCheckAll: (rows) => {
                    for (let i = 0; i < rows.length; i++) {
                        this.searchedRow.push(rows[i])
                    }
                },
                onUncheckAll: (rows) => {
                    this.searchedRow = []
                },
                onCheck: (row, $element) => {
                    this.searchedRow.push(row)
                },
                onUncheck: (row, $element) => {
                    this.searchedRow.splice($.inArray(row, this.searchedRow), 1);
                },
                onPageChange: (number, size) => {
                    this.searchedRow = []
                }
            });
            common.changeTableClass(this.tableMateriel)
        }

    },
    async mounted() {
        this.tableProduct = $('#tableProduct');
        this.tableOrderList = $('#tableOrderList');
        this.tableMateriel = $('#tableMateriel');
        await this.getPagePara();
        await this.initTableProduct();
        common.reSizeCall();
    },
}
</script>
<style scoped>
    .m_t{
        margin-top: 15px;
        /*margin-left: 10px;*/
        /*margin-right: 10px;*/
    }
</style>
