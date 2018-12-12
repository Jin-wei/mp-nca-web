<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">产品规划列表</a></li>
        <li class="active">产品规划详情</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <span>产品编码：{{queryData.materielCode}}&emsp;产品名称：{{queryData.materielName}}</span>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">产品规划详情</h4>
                </div>
                <div class="form-inline panel-toolbar" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索物料编码、名称" v-model="searchText[tabIndex]" style="width: 180px">
                        </div>
                        <div class="form-group">
                            <button id="queryConfirm" class="btn btn-info" @click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                    <div class="form-group pull-right" v-if="queryData.validState == 0">
                        <button id="showAddMateriel" class="btn btn-info" @click="showAddMateriel">{{tabButton[tabIndex]}}
                        </button>
                        <button class="btn btn-info" @click="submitProductPlanVerify" v-show="tabIndex === 0">提交
                        </button>
                    </div>
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
                            <table id="tableDetail"></table>
                        </div>
                        <div class="tab-pane fade" id="tabRelated">
                            <table id="tableRelated1"></table>
                        </div>
                        <div class="tab-pane fade" id="tabRemain">
                            <table id="tableRelated2"></table>
                        </div>
                        <div class="tab-pane fade" id="tabProcedure">
                            <table id="tableProcedure"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">添加物料</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-10">
                        <input class="form-control" v-model="searchData" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-2">
                        <button type="button" class="btn btn-info" @click="addProductMateriel">保存</button>
                    </div>
                    <table id="tableMateriel"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddPlanMaterielModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">{{tabButton[tabIndex]}}</h4>
                </div>
                <div class="modal-body">
                    <table class="table table-bordered">
                        <thead>
                        <tr>
                            <th>物料编码</th>
                            <th>物料名称</th>
                            <th>物料型号</th>
                            <th>单位</th>
                            <th>选择</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="item in planMaterielList">
                            <td>{{item.materiel_code}}</td>
                            <td>{{item.materiel_name}}</td>
                            <td>{{item.materiel_format}}</td>
                            <td>{{item.materiel_unit_name}}</td>
                            <td>
                                <button class="btn btn-info btn-xs m-r-5" @click="onRelatedMaterielSelect(item)">选择</button>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddVerifyModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">选择验证项</h4>
                </div>
                <div class="modal-body">
                    <table class="table table-bordered">
                        <thead>
                        <tr>
                            <th>选择</th>
                            <th>评审项目</th>
                            <th>评审责任描述</th>
                            <th>责任人</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="item in planVerifyList">
                            <td>
                                <input type="checkbox" class="form-check-input" v-model="item.checked">
                            </td>
                            <td>{{item.require_name}}</td>
                            <td>{{item.require_description}}</td>
                            <td>{{item.require_user_name}}</td>
                        </tr>
                        </tbody>
                    </table>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info" @click="addProductPlanVerify">保存</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddLevelMaterielModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">上级投料</h4>
                </div>
                <div class="modal-body">
                    <table class="table table-bordered">
                        <thead>
                        <tr>
                            <th>物料编码</th>
                            <th>物料名称</th>
                            <th>选择</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="item in levelMaterielList">
                            <td>{{item.materiel_code}}</td>
                            <td>{{item.materiel_name}}</td>
                            <td>
                                <button class="btn btn-info btn-xs m-r-5" @click="onLevelMaterielSelect(item)">选择</button>
                            </td>
                        </tr>
                        </tbody>
                    </table>
                </div>
                <!--<div class="modal-footer">
                    <button type="button" class="btn btn-info" @click="addProductPlanVerify">保存</button>
                </div>-->
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddProcedureModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增生产工序</h4>
                </div>
                <form @submit.prevent="addProductProcedure" id="formProcedure">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label>生产工序名称:</label>
                            <select class="form-control select2" multiple style="width:100%" id="product_procedure"
                                    data-parsley-required="true"></select>
                        </div>
                        <div class="form-group col-xs-6">
                            <label>优先级:</label>
                            <input type="number" class="form-control" v-model="workRow.priority"
                                   data-parsley-required="true" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-12">
                            <label>对应物料编码:</label>
                            <select class="form-control select2" multiple style="width:100%" id="product_materiel"
                                    data-parsley-required="true"></select>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
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
            searchText: [],
            searchData: null,
            queryData: {},
            tabIndex: 0,
            tabButton: [
                '增加物料', '增加联产品', '增加边余料', '增加工序'
            ],
            tableDetail: [],
            planMaterielList: [],
            levelMaterielList: [],
            planVerifyList: []
        }
    },
    name: 'NCAProductPlanDetailControl',
    methods: {
        async getPagePara() {
            try {
                let response = await this.$http.post(apiUrl + 'initProductPlanDetail', {});
                this.pagePara = response.data.info;
                common.initSelect2Editable($('#product_procedure'), this.pagePara.procedureInfo);
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
        async submitProductPlanVerify() {
            try {
                let response = await this.$http.post(apiUrl + 'submitProductPlanVerify', {
                    product_id: this.queryData.productId,
                    materiel_id: this.queryData.materielId
                });
                common.dealSuccessCommon('提交成功');
                this.$router.back();
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showAddMateriel() {
            switch (this.tabIndex) {
                case 0:
                case 1:
                case 2:
                    await this.initTableMateriel();
                    $('#AddMaterielModal').modal('show');
                    this.searchedRow = [];
                    $("input[type=checkbox]").each(function() {
                        $(this).prop("checked", false);
                    });
                    break;

                case 3:
                    this.workRow = {};
                    $('#formProcedure').parsley().reset();
                    $('#product_procedure').val(null).trigger('change');
                    $('#product_materiel').val(null).trigger('change');
                    await this.getProductMateriel();
                    $('#AddProcedureModal').modal('show');
                    break;
            }
        },
        async addProductMateriel() {
            const params = {
                product_plan_id: this.queryData.productId,
                materiel_id: this.queryData.materielId
            };
            console.log('addProductMateriel:', params);

            let apiPath = 'addProductMateriel';
            switch (this.tabIndex) {
                case 0:
                    apiPath = 'addProductMateriel';
                    params.searchedRow = this.searchedRow;
                    await this.$http.post(apiUrl + apiPath, params);
                    this.tableDetail[this.tabIndex].bootstrapTable("refresh");
                    $('#AddMaterielModal').modal('hide');
                    break;

                case 1:
                case 2:
                    apiPath = 'addProductRelated';
                    params.prd_type = this.tabIndex;
                    params.searchedRow = this.searchedRow;
                    await this.$http.post(apiUrl + apiPath, params);
                    this.tableDetail[this.tabIndex].bootstrapTable("refresh");
                    $('#AddMaterielModal').modal('hide');
                    break;
            }
        },
        async onRelatedMaterielSelect(data) {
            console.log('onRelatedMaterielSelect:', data);
            try {
                this.workRow.rlt_materiel_code = data.materiel_id;
                let method = 'modifyProductRelated';
                if (this.tabIndex > 2) {
                    method = 'modifyProductProcedure';
                }
                await this.$http.post(apiUrl + method, {
                    old: this.workRow,
                    new: this.workRow
                });
                common.dealSuccessCommon('设置成功');
                this.tableDetail[this.tabIndex].bootstrapTable("refresh");
                $('#AddPlanMaterielModal').modal('hide');
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async addProductProcedure() {
            try {
                if ($('#formProcedure').parsley().isValid()) {
                    this.workRow.product_plan_id = this.queryData.productId;
                    this.workRow.materiel_id = this.queryData.materielId;
                    this.workRow.procedure_id = common.getSelect2Val('product_procedure');
                    this.workRow.rlt_materiel_code = common.getSelect2Val('product_materiel');

                    await this.$http.post(apiUrl + 'addProductProcedure', this.workRow);
                    common.dealSuccessCommon('增加成功');
                    this.tableDetail[this.tabIndex].bootstrapTable("refresh");
                    $('#AddProcedureModal').modal('hide')
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showLevelMateriel(prdLevel) {
            try {
                const result = await this.$http.post(apiUrl + 'searchProductDetail', {
                    materiel_id: this.queryData.materielId,
                    prd_level: prdLevel - 1
                });
                if (result.data.info.total > 0) {
                    this.levelMaterielList = result.data.info.rows;
                    $('#AddLevelMaterielModal').modal('show');
                } else {
                    common.dealWarningCommon(`没有${prdLevel - 1}级投料`);
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showProductPlanMateriel(materielId) {
            try {
                const postData = {
                    materiel_id: this.queryData.materielId
                };
                if (materielId) {
                    postData.dtl_materiel_id = materielId
                }
                const result = await this.$http.post(apiUrl + 'searchProductDetail', postData);
                this.planMaterielList = result.data.info.rows;
                this.planMaterielList = this.planMaterielList.map(item => {
                    for (const unit of this.pagePara.unitInfo) {
                        if (item.materiel_unit === unit.id) {
                            item.materiel_unit_name = unit.text;
                            break;
                        }
                    }
                    return item
                });
                $('#AddPlanMaterielModal').modal('show');
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async onLevelMaterielSelect(data) {
            try {
                this.workRow.level_materiel_id = data.materiel_id;
                await this.$http.post(apiUrl + 'modifyProductMateriel', {
                    old: this.workRow,
                    new: this.workRow
                });
                common.dealSuccessCommon('配置成功');
                this.tableDetail[0].bootstrapTable("refresh");
                $('#AddLevelMaterielModal').modal('hide');
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async addProductPlanVerify() {
            try {
                const postData = {
                    product_plan_id: this.queryData.productId,
                    verify_type: this.tabIndex + 1,
                    searchedRow: this.planVerifyList.filter(item => (item.checked))
                };

                switch (this.tabIndex) {
                    case 0:
                        postData.product_ppd_id = this.workRow.product_dtl_id;
                        break;

                    case 1:
                    case 2:
                        postData.product_ppd_id = this.workRow.product_rlt_id;
                        break;

                    case 3:
                        postData.product_ppd_id = this.workRow.product_procedure_id;
                        break;
                }

                await this.$http.post(apiUrl + 'bindPlanVerifyForMateriel', postData);
                common.dealSuccessCommon('配置成功');
                this.tableDetail[0].bootstrapTable("refresh");
                $('#AddVerifyModal').modal('hide')
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        queryConfirm() {
            this.tableDetail[this.tabIndex].bootstrapTable("refresh")
        },
        searchMate() {
            this.searchedRow = [];
            this.tableMateriel.bootstrapTable("refresh")
        },
        async initTableDetail(type) {
            window.tableEvents = {
                'click .level_materiel': async (e, value, row, index) => {
                    const { prd_level, level_materiel_id } = row;
                    console.log('prd_level:', prd_level, 'level_materiel_id:', level_materiel_id);
                    if (prd_level) {
                        if (Number(prd_level) === 2 && level_materiel_id) {
                            common.dealWarningCommon('上级投料已设置');
                        } else {
                            this.workRow = row;
                            await this.showLevelMateriel(prd_level);
                        }
                    } else {
                        common.dealWarningCommon('请输入产品层级');
                    }
                },
                'click .verify_setting': async (e, value, row, index) => {
                    this.workRow = row;
                    const result = await this.$http.post(apiUrl + 'searchPlanMaterielVerify', {
                        require_type: 5,
                        product_ppd_id: row.product_dtl_id,
                        verify_type: type + 1
                    });
                    this.planVerifyList = result.data.info.requireList;
                    this.planVerifyList = this.planVerifyList.map(item => {
                        for (const verifyData of result.data.info.verifyList) {
                            if (verifyData.require_id === item.require_id) {
                                item.checked = true;
                                break;
                            }
                        }
                        return item
                    });
                    $('#AddVerifyModal').modal('show')
                },
                'click .delete_materiel': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProductMateriel', this.tableDetail[type], row, 'product_dtl_id', function(){})
                }
            };

            let tableColumns = [];
            if (this.queryData.validState > 0) {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    // common.BTRowFormatEdSelect2Disabled(this, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'prd_level', '产品层级', 'prdLevelInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'level_materiel_id', '上级投料', 'materielLevelInfo'),
                    common.BTRowFormatAlignRight('design_number', '设计投料数量'),
                    common.BTRowFormatAlignRight('loss_rate', '损耗率%'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_state_management', '状态分类', 'stateManagementInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_source', '来源分类', 'materielSource'),
                    common.BTRowFormatEdSelect2Disabled(this, 'workshop_id', '对应车间', 'departmentInfo')
                ];
            } else {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    // common.BTRowFormatEdSelect2Disabled(this, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEdSelect2(this, 'prd_level', '产品层级', 'prdLevelInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'level_materiel_id', '上级投料', 'materielLevelInfo'),
                    common.BTRowFormatEditableRight('design_number', '设计投料数量'),
                    common.BTRowFormatEditableRight('loss_rate', '损耗率%'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_state_management', '状态分类', 'stateManagementInfo'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_source', '来源分类', 'materielSource'),
                    common.BTRowFormatEdSelect2(this, 'workshop_id', '对应车间', 'departmentInfo'),
                    common.BTRowFormatWithFE('', '操作', () => {
                        return `<button class="btn btn-info btn-xs m-r-5 level_materiel">上级投料</button>
                                <button class="btn btn-info btn-xs m-r-5 verify_setting">审核配置</button>
                                <button class="btn btn-info btn-xs m-r-5 delete_materiel btn-info-delete">删除</button>`
                    }, tableEvents)
                ];
            }

            this.tableDetail[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductDetail',
                queryParams: (params) => {
                    params.materiel_id = this.queryData.materielId;
                    params.search_text = this.searchText[0];
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: tableColumns,
                idField: 'product_dtl_id',
                uniqueId: 'product_dtl_id',
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
                    if (field === 'prd_level') {
                        if (row.prd_level === '2') {
                            row.level_materiel_id = this.queryData.materielId
                        } else {
                            row.level_materiel_id = null;
                        }
                    }
                    common.rowModifyWithT(this, apiUrl + 'modifyProductMateriel', row, 'product_dtl_id', this.tableDetail[type]);
                }
            });
            common.changeTableClass(this.tableDetail[type])
        },
        async initTableRelated(type) {
            window.tableEvents = {
                'click .materiel_setting': async (e, value, row, index) => {
                    this.workRow = row;
                    await this.showProductPlanMateriel(row.materiel_id);
                },
                'click .verify_setting': async (e, value, row, index) => {
                    this.workRow = row;
                    const result = await this.$http.post(apiUrl + 'searchPlanMaterielVerify', {
                        require_type: 5,
                        product_ppd_id: row.product_rlt_id,
                        verify_type: type + 1
                    });
                    this.planVerifyList = result.data.info.requireList;
                    this.planVerifyList = this.planVerifyList.map(item => {
                        for (const verifyData of result.data.info.verifyList) {
                            if (verifyData.require_id === item.require_id) {
                                item.checked = true;
                                break;
                            }
                        }
                        return item
                    });
                    $('#AddVerifyModal').modal('show')
                },
                'click .delete_materiel': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '产品删除', apiUrl + 'deleteProductRelated', this.tableDetail[type], row, 'product_rlt_id', function(){})
                }
            };

            const codeLabel = ['', '产品编码', '物料编码'];
            const nameLabel = ['', '产品名称', '物料名称'];
            let tableColumns = [];
            if (this.queryData.validState > 0) {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', codeLabel[type]),
                    common.BTRowFormatAlignLeft('materiel_name', nameLabel[type]),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('prd_number', '数量'),
                    common.BTRowFormatEdSelect2Disabled(this, 'rlt_materiel_code', '对应物料编码', 'materielInfo')
                ];
            } else {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', codeLabel[type]),
                    common.BTRowFormatAlignLeft('materiel_name', nameLabel[type]),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatEditableRight('prd_number', '数量'),
                    common.BTRowFormatEdSelect2Disabled(this, 'rlt_materiel_code', '对应物料编码', 'materielInfo'),
                    common.actFormatter('act', () => {
                        return [
                            '<button class="btn btn-info btn-xs m-r-5 materiel_setting">对应物料</button>',
                            '<button class="btn btn-info btn-xs m-r-5 verify_setting">审核配置</button>',
                            '<button class="btn btn-info btn-xs m-r-5 delete_materiel btn-info-delete">删除</button>'
                        ].join('')
                    }, tableEvents)
                ];
            }

            this.tableDetail[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductRelated',
                queryParams: (params) => {
                    params.prd_type = type;
                    params.materiel_id = this.queryData.materielId;
                    params.search_text = this.searchText[this.tabIndex];
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: tableColumns,
                idField: 'product_rlt_id',
                uniqueId: 'product_rlt_id',
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
                    common.rowModifyWithT(this, apiUrl + 'modifyProductRelated', row, 'product_rlt_id', this.tableDetail[type]);
                }
            });
            common.changeTableClass(this.tableDetail[type])
        },
        async initTableProcedure(type){
            window.tableEvents = {
                'click .materiel_setting': async (e, value, row, index) => {
                    this.workRow = row;
                    await this.showProductPlanMateriel();
                },
                'click .verify_setting': async (e, value, row, index) => {
                    this.workRow = row;
                    const result = await this.$http.post(apiUrl + 'searchPlanMaterielVerify', {
                        require_type: 5,
                        product_ppd_id: row.product_procedure_id,
                        verify_type: type + 1
                    });
                    this.planVerifyList = result.data.info.requireList;
                    this.planVerifyList = this.planVerifyList.map(item => {
                        for (const verifyData of result.data.info.verifyList) {
                            if (verifyData.require_id === item.require_id) {
                                item.checked = true;
                                break;
                            }
                        }
                        return item
                    });
                    $('#AddVerifyModal').modal('show')
                },
                'click .delete_procedure': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '工序删除', apiUrl + 'deleteProductProcedure', this.tableDetail[type], row, 'product_procedure_id', function(){})
                }
            };

            let tableColumns = [];
            if (this.queryData.validState > 0) {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('procedure_name', '工序名称'),
                    common.BTRowFormat('priority', '优先级'),
                    common.BTRowFormatEdSelect2Disabled(this, 'rlt_materiel_code', '对应物料编码', 'materielInfo')
                ];
            } else {
                tableColumns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEditableLeft('procedure_name', '工序名称'),
                    common.BTRowFormatEditable('priority', '优先级'),
                    common.BTRowFormatEdSelect2Disabled(this, 'rlt_materiel_code', '对应物料编码', 'materielInfo'),
                    common.actFormatter('act', () => {
                        return [
                            '<button class="btn btn-info btn-xs m-r-5 materiel_setting">对应物料</button>',
                            '<button class="btn btn-info btn-xs m-r-5 verify_setting">审核配置</button>',
                            '<button class="btn btn-info btn-xs m-r-5 delete_procedure btn-info-delete">删除</button>'
                        ].join('')
                    }, tableEvents)
                ];
            }

            this.tableDetail[type].bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductProcedure',
                queryParams: (params) => {
                    params.prd_type = type;
                    params.materiel_id = this.queryData.materielId;
                    params.search_text = this.searchText[this.tabIndex];
                    return JSON.stringify(params)
                },
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: tableColumns,
                idField: 'product_procedure_id',
                uniqueId: 'product_procedure_id',
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
                    common.rowModifyWithT(this, apiUrl + 'modifyProductProcedure', row, 'product_procedure_id', this.tableDetail[type]);
                }
            });
            common.changeTableClass(this.tableDetail[type])
        },
        async initTableMateriel() {
            this.tableMateriel.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMateriel',
                queryParams: (params) => {
                    params.searchData = this.searchData;
                    params.tableName = 'productDetail';
                    params.materiel_id = this.queryData.materielId;
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
                    // common.BTRowFormat('domain_name', '所属机构')
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                clickToSelect: true,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 25,50,100],
                locale: 'zh-CN',
                onCheckAll: (rows) => {
                    this.searchedRow = [
                        ...rows
                    ];
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
        this.queryData = this.$route.query;
        this.tableDetail[0] = $('#tableDetail');
        this.tableDetail[1] = $('#tableRelated1');
        this.tableDetail[2] = $('#tableRelated2');
        this.tableDetail[3] = $('#tableProcedure');
        this.tableMateriel = $('#tableMateriel');
        this.tableVerify = $('#tableVerify');
        await this.getPagePara();
        await this.getProductMateriel();
        await this.initTableDetail(0);
        await this.initTableRelated(1);
        await this.initTableRelated(2);
        await this.initTableProcedure(3);
        common.reSizeCall();
    }
}
</script>
<style scoped>
    .m_b {
        margin-bottom: 15px;
    }
</style>
