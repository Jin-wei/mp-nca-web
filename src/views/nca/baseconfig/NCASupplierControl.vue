<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">系统管理</a></li>
        <li class="active">供应商列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">供应商列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="searchText" placeholder="搜索编号、名称、地址等" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">增加
                      </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="table"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增供应商列表</h4>
                </div>
                <form @submit.prevent="addDm" id="formA">
                    <div class="modal-body row">
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">机构</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<select class="form-control picker-width" data-parsley-required="true" v-model="workRow.domain_id">-->
                                    <!--<option v-bind:value="supplier.domain_id" v-for="supplier in suppliers">{{supplier.domain_name}}</option>-->
                                <!--</select>-->
                            <!--</div>-->
                        <!--</div>-->
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>供应商编号</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>供应商名称</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">地址</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_address" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">联系人</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_contact" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">手机</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_phone" data-parsley-phone="true">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">经营范围</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_description" maxlength="100" data-parsley-maxlength="100">
                            </div>
                        </div>
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">采购比例</label>-->
                            <!--<div class="col-sm-7 p_r">-->
                                <!--<input  onkeyup="value=value.replace(/[^\d\.]/g,'')" onblur="value=value.replace(/[^\d\.]/g,'')" oninput="if(value.length>5)value=value.slice(0,5)"-->
                                        <!--class="form-control" id="supplier_proportion" v-model="workRow.supplier_proportion">-->
                                <!--<span class="p_a">%</span>-->
                            <!--</div>-->
                        <!--</div>-->
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">传真</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.supplier_fax">
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12" style="padding-left: 15px;width: 600px">
                                <label class="col-sm-2 control-label">备注</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" v-model="workRow.supplier_remarks" maxlength="200" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCASupplierControl?method='
import Constant from '../../../assets/js/constant'

export default {
    data: function() {
        return {
            pagePara: '',
            workRow: {},
            oldRow: '',
            suppliers:[],
            searchText: null,
            masterForm: null,
            masterTable: null,
            modal: null
        }
    },
    name: 'supplierControl',
    mounted: function() {
        this.masterForm = $('#formA')
        this.masterTable = $('#table')
        this.modal = $('#AddModal')
        this.initPage()
    },
    methods: {
        queryParams: function (params) {
            params.suppliersearch_text = this.searchText;
            return JSON.stringify(params)
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams)
        },
        addM: function(event) {
            this.workRow = {}
            this.masterForm.parsley().reset()
            this.modal.modal('show')
        },
        addDm: function(event) {
            if (this.masterForm.parsley().isValid()) {
                this.workRow.supplier_proportion = this.workRow.supplier_proportion 
                this.$http.post(apiUrl + 'add', this.workRow).then((response) => {
                    let retData = response.data.info
                    this.masterTable.bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                    this.masterTable.bootstrapTable('resetView')
                    this.workRow = {}
                    common.dealSuccessCommon('增加成功')
                    this.modal.modal('hide')
                    console.log('add success')
                    this.masterTable.bootstrapTable("refresh", this.queryParams)
                }, (response) => {
                    common.dealErrorCommon(this, response)
                })
            }
        },
        initTable: function () {
            window.tableEvents = {
                'click .show_detail': (e, value, row, index) => {
                    if (row.domain_type === '2') {
                        this.$router.push({
                            path: '/nca/baseconfig/NCASupplierProduceControl',
                            query: {
                                supplierId: row.supplier_id
                            }
                        })
                    } else {
                        this.$router.push({
                            path: '/nca/baseconfig/NCASupplierMaterielControl',
                            query: {
                                supplierId: row.supplier_id
                            }
                        })
                    }
                },
                'click .tableEdit': (e, value, row, index) => {
                    Constant.setData('userGroup', this.pagePara.userGroup);
                    this.$router.push({
                        path: '/nca/baseconfig/NCASupplierTeamControl',
                        query: row
                    })
                }
            };
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('supplier', '供应商编号', (value, row) => {
                        return `<a class="show_detail" style="cursor: pointer;">${value}</a>`;
                    }, tableEvents),
                    common.BTRowFormatEditableLeft('supplier_name', '供应商名称'),
                    // common.BTRowFormat('domain_name', '机构'),
                    /*common.BTRowFormatEditable('supplier_address', '地址'),*/
                    common.BTRowFormatEdTextareaWidthAlignLeft('supplier_address', '地址',150,10),
                    common.BTRowFormatEditableLeft('supplier_contact', '联系人'),
                    common.BTRowFormatEditable('supplier_phone', '联系方式'),
                    // common.BTRowFormatEditable('supplier_description', '经营范围'),
                    common.BTRowFormatEditable('supplier_fax', '传真'),
                    common.BTRowFormatAlignRight('supplier_proportion', '采购比例'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('supplier_remarks', '备注',100,7),
                    common.BTRowFormatEdSelect2(this, 'state', '状态', 'stateInfo'),
                    common.actFormatter('act', (value, row) => {
                        if (row.domain_type !== this.pagePara.domainTypeInfo[2].id) {
                            return `<a class="btn btn-xs btn-info tableEdit">供应商登录账号编辑</a>`
                        }
                    }, tableEvents)
                ],
                idField: 'supplier_id',
                uniqueId: 'supplier_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    common.DynamicEditableByDomain2('2', $('#table'))
                },
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modify', row, 'supplier_id', this.masterTable)
                }
            })
            common.changeTableClass(this.masterTable)
        },
        initPage: async function() {
            let response = await this.$http.post(apiUrl + 'init', {})
            let retData = response.data.info
            this.suppliers = retData.suppliers;
            this.pagePara = retData;
            this.initTable()
            this.masterForm.parsley()
            common.reSizeCall()
            console.log('init success')
        }
    }
}
</script>
<style scoped>
.p_r{
     position:relative;
    }
.p_a{
     position:absolute;
     top:8px;
     right:0px;
    }
</style>
