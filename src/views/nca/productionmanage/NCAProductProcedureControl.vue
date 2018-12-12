<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">生成管理</a></li>
        <li class="active">生产工序列表</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">生产工序列表</h4>
                </div>
                <div class="form-inline panel-toolbar" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input type="text" class="form-control" placeholder="搜索生产工序编码、名称" v-model="searchText" style="width: 180px">
                        </div>
                        <div class="form-group">
                            <button id="queryConfirm" class="btn btn-info" @click="queryConfirm">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                    <div class="form-group  pull-right">
                        <button id="showAddProcedure" class="btn btn-info" @click="showAddProcedure">增加
                        </button>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="margin-top: 0px">
                    <table id="tableProcedure"></table>
                </div>
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
                <form @submit.prevent="saveProcedure" id="formProcedure">
                    <div class="modal-body row">
                        <div class="form-group col-xs-6">
                            <label class=""><span class="table-required">*</span>生产工序名称:</label>
                            <input class="form-control " v-model="workRow.procedure_name" data-parsley-required="true" maxlength="30" data-parsley-maxlength="30">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>生产工序分类:</label>
                            <select class="form-control select2" multiple style="width:100%" id="procedure_type"
                                    data-parsley-required="true"></select>
                        </div>
                        <div class="form-group col-xs-6">
                            <label>生产工序工价:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_cost" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>最低日工资:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_pay" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>保底计算量:</label>
                            <input type="number" class="form-control" v-model="workRow.procedure_calc" data-parsley-type="number">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>对应主生产设备:</label>
                            <select class="form-control select2" multiple style="width:100%" id="master_device"
                                    data-parsley-required="true"></select>
                        </div>
                        <div class="form-group col-xs-12">
                            <label>对应辅生产设备:</label>
                            <select class="form-control select2" multiple style="width:100%" id="slave_device"
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
const apiUrl = '/api/nca/baseconfig/NCAProductProcedureControl?method=';

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
            tableProcedure: null,
            tableMateriel: null
        }
    },
    name: 'NCAProductProcedureControl',
    methods: {
        async getPagePara(event) {
            $('#formProcedure').parsley();
            try {
                let response = await this.$http.post(apiUrl + 'initProductionProcedure', {});
                this.pagePara = response.data.info;
                common.initSelect2Editable($('#procedure_type'), this.pagePara.procedureTypeInfo);
                common.initSelect2Editable($('#master_device'), this.pagePara.deviceInfo);
                common.initSelect2Editable($('#slave_device'), this.pagePara.deviceInfo);
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        async showAddProcedure() {
            this.workRow = {};
            $('#formProcedure').parsley().reset();
            $('#procedure_type').val(null).trigger('change');
            $('#master_device').val(null).trigger('change');
            $('#slave_device').val(null).trigger('change');
            $('#AddProcedureModal').modal('show')
        },
        async saveProcedure() {
            try {
                if ($('#formProcedure').parsley().isValid()) {
                    this.workRow.procedure_type = common.getSelect2Val('procedure_type');
                    this.workRow.procedure_master_device = common.getSelect2Val('master_device');
                    this.workRow.procedure_slave_device = common.getSelect2Val('slave_device');

                    await this.$http.post(apiUrl + 'addProductionProcedure', this.workRow);
                    common.dealSuccessCommon('增加成功');
                    this.tableProcedure.bootstrapTable("refresh");
                    $('#AddProcedureModal').modal('hide')
                }
            } catch (error) {
                common.dealErrorCommon(this, error);
            }
        },
        queryConfirm(event) {
            this.tableProcedure.bootstrapTable("refresh")
        },
        async initTableProcedure(event){
            window.tableEvents = {
                'click .delete_procedure': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '生产工序删除', apiUrl + 'deleteProductionProcedure', this.tableProcedure, row, 'procedure_id', function(){})
                }
            };
            this.tableProcedure.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProductionProcedure',
                queryParams: (params) => {
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
                    common.BTRowFormat('procedure_code', '生产工序编号'),
                    common.BTRowFormatAlignLeft('procedure_name', '生产工序名称'),
                    common.BTRowFormatEdSelect2(this, 'procedure_type', '生产工序分类', 'procedureTypeInfo'),
                    common.BTRowFormatEdPrice('procedure_cost', '生产工序工价',common.priceFormat),
                    common.BTRowFormatEdPrice('procedure_pay', '最低日工资',common.priceFormat),
                    common.BTRowFormatEditableRight('procedure_calc', '保底计算量'),
                    common.BTRowFormatEdSelectAlignLeft(this, 'procedure_master_device', '对应主生产设备', 'deviceInfo'),
                    common.BTRowFormatEdSelectAlignLeft(this, 'procedure_slave_device', '对应辅生产设备', 'deviceInfo'),
                    common.actFormatter('act', () => {
                        return `<button class="btn btn-info btn-xs m-r-5 delete_procedure">删除</button>`
                    }, tableEvents)
                ],
                idField: 'procedure_id',
                uniqueId: 'procedure_id',
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
                    common.rowModifyWithT(this, apiUrl + 'modifyProductionProcedure', row, 'procedure_id', this.tableProcedure);
                }
            });
            common.changeTableClass(this.tableProcedure)
        }
    },
    async mounted() {
        this.tableProcedure = $('#tableProcedure');
        await this.getPagePara();
        await this.initTableProcedure();
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
