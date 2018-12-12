<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">长期资产管理</a></li>
            <li class="active">待摊资产项目列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default"
                               data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">待摊资产项目列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索待摊资产项目编号、待摊资产项目名称"
                                           style="width: 300px;">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div id="set_table">
                            <table id="setTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 v-if="saveId !== ''" class="modal-title">修改待摊资产项目</h4>
                        <h4 v-else class="modal-title">新增待摊资产项目列表</h4>
                        <!--<h4 class="modal-title"> 查看待摊资产项目列表</h4>-->
                    </div>
                    <form @submit.prevent="saveSaleOrder" id="formC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>项目名称</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="amortize_name" data-parsley-group="block-1"
                                               v-model="workRow.amortize_name" data-parsley-required="true"
                                               maxlength="100" data-parsley-maxlength="100">
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label" style="padding-left:0px"><span
                                            class="table-required">*</span>预计使用年限</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="amortize_agelimit" data-parsley-group="block-1"
                                               v-model="workRow.amortize_agelimit" maxlength="3"
                                               data-parsley-maxlength="3" data-parsley-required="true"
                                               data-parsley-type="number">
                                    </div>
                                </div>
                            </div>

                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="amortize_departmant_id"
                                               data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="amortize_manager" data-parsley-required="true"
                                               disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(2)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>推销方法</label>
                                    <div class="col-md-10">
                                        <select class="form-control select2" id="amortize_way"
                                                data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label">项目描述</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="amortize_remark"
                                               v-model="workRow.amortize_remark" maxlength="100"
                                               data-parsley-maxlength="100">
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button v-show="_self.saveId !== ''" type="submit" class="btn btn-info" id="submitAct"
                                    v-on:click="submitAct">再次提交
                            </button>
                            <button v-if="_self.saveId === ''" type="submit" class="btn btn-info" id="saveAct">提交
                            </button>
                            <button v-else type="submit" class="btn btn-info" id="comfirmAct">修改</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>


        <div class="modal fade" id="ShowModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <!--<h4 v-if="saveId !== ''" class="modal-title">修改待摊资产项目</h4>-->
                        <!--<h4 v-else class="modal-title">新增待摊资产项目列表</h4>-->
                        <h4 class="modal-title"> 查看待摊资产项目列表</h4>
                    </div>
                    <form @submit.prevent="saveSet" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>项目名称</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_amortize_name" data-parsley-group="block-1"
                                               v-model="workRow.amortize_name" data-parsley-required="true"
                                               maxlength="100" data-parsley-maxlength="100">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group" style="margin-top: 10px">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>预算总金额</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_amortize_budget"
                                               data-parsley-group="block-1" v-model="workRow.amortize_budget"
                                               data-parsley-required="true" maxlength="100" data-parsley-maxlength="100"
                                               disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label" style="padding-left:0px"><span
                                            class="table-required">*</span>预计使用年限</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_amortize_agelimit"
                                               data-parsley-group="block-1" v-model="workRow.amortize_agelimit"
                                               maxlength="3" data-parsley-maxlength="3" data-parsley-required="true"
                                               data-parsley-type="number">
                                    </div>
                                </div>
                            </div>

                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="show_amortize_departmant_id"
                                               data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="show_amortize_manager"
                                               data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button"
                                               v-on:click="teamP(2)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>推销方法</label>
                                    <div class="col-md-10">
                                        <select class="form-control select2" id="show_amortize_way"
                                                data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="amortize_refuse_remark"
                                               v-model="workRow.amortize_refuse_remark" maxlength="100"
                                               data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label">项目描述</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="show_amortize_remark"
                                               v-model="workRow.amortize_remark" maxlength="100"
                                               data-parsley-maxlength="100">
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span
                                            class="table-required">*</span>验收驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="amortize_check_refuse_remark"
                                               v-model="workRow.amortize_check_refuse_remark" maxlength="100"
                                               data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button v-show="_self.workRow.amortize_project_state === 3" type="submit" class="btn btn-info" id="show_submitAct"
                                    v-on:click="submitAct">再次提交
                            </button>
                            <!--<button v-if="_self.workRow.amortize_project_state !== 3" type="submit" class="btn btn-info" id="show_saveAct">-->
                            <!--</button>-->
                            <button v-show="_self.workRow.amortize_project_state === 3" type="submit" class="btn btn-info" id="Act">修改</button>
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
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    import userNewDepartment from '../baseconfig/userNewDepartment.vue';
    import userSelectDialog from '../../../components/common/userSelectDialog.vue'

    export default {
        data: function () {
            return {
                pagePara: {},
                pagePara2: {},
                pagePara3: {},
                tableType: 1,
                QRHtml: '',
                workRow: {},
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                searchedRow: [],
                saveId: '',
                usergroupId: '',
                users: [],
                eventHub: new Vue(),
                index: '',
                mulityFlag: '',
            }
        },
        name: 'NCAAmortizeControl',
        components: {
            DomainChooseHeader,
            userNewDepartment,
            userSelectDialog
        },
        mounted: function () {
            let _self = this;
            let $setTable = $('#setTable');

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-xs btn-info m-r-5 tableDelete" style="cursor: pointer;">删除</a>',
                    '<a class="table_edit btn btn-xs btn-info m-r-5 " style="cursor: pointer;">查看</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 edit_detail btn-info-table">编辑</a>',
                ].join('')
            }

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }

            window.tableEvents = {
                'click .show_detail': function (e, value, row, index) {

                    if (row.amortize_project_state === 2) {
                        _self.$router.push({
                            path: '/nca/longtermassets/NCAAmortizeDetailControl',
                            query: {
                                amortize_id: row.amortize_id
                            }
                        })
                    } else {
                        return common.dealPromptCommon('项目未通过，不能显示详情')
                    }
                },
                'click .tableDelete': function (e, value, row, index) {
                    if (row.amortize_project_state === 3) {
                        common.dealConfrimCommon('您确定删除么', function () {
                            _self.$http.post(apiUrl + 'delete', {amortize_id: row.amortize_id}).then((response) => {
                                $setTable.bootstrapTable("refresh");
                            }, (response) => {
                            });
                        });
                    } else if (row.amortize_project_state === 1) {
                        return common.dealPromptCommon('该项目待审核，不能删除')
                    } else {
                        return common.dealPromptCommon('该项目已通过，不能删除')
                    }
                },
                'click .table_edit': async function (e, value, row, index) {

                    $('#ShowModal').modal('show');
                    // if(row.amortize_project_state === 3){
                    _self.saveId = row.amortize_id;
                    _self.workRow = $.extend(true, {}, row)
                    let departmentId = common.getSelect2Val('amortize_departmant_id')
                    let response = await _self.$http.post(apiUrl + 'getManagerInfo', {departmant_id: departmentId});
                    let retData = response.data.info;
                    let amortizeId = common.getSelect2Val('amortize_manager')
                    common.initSelect2($('#amortize_manager'), retData.marangerInfo);

                    row.amortize_agelimit = _self.workRow.amortize_agelimit
                    $('#show_amortize_budget').val(_self.workRow.amortize_budget).trigger('change');
                    $('#show_amortize_remark').val(_self.workRow.amortize_remark).trigger('change');
                    $('#show_amortize_departmant_id').val(_self.workRow.department_name).trigger('change');
                    $('#show_amortize_manager').val(_self.workRow.amortize_manager).trigger('change');
                    $('#show_amortize_way').val(_self.workRow.amortize_way).trigger('change');
                    $('#amortize_refuse_remark').val(_self.workRow.amortize_refuse_remark).trigger('change');
                    $('#show_amortize_agelimit').val(_self.workRow.amortize_agelimit).trigger('change');
                    $('#amortize_check_refuse_remark').val(_self.workRow.amortize_check_refuse_remark).trigger('change');
                    // $('#AddModal').modal('show');
                    // }else if (row.amortize_project_state ===1) {
                    //     return common.dealPromptCommon('该项目待审核，不能编辑')
                    // }else {
                    //     return common.dealPromptCommon('该项目已通过，不能编辑')
                    // }
                },
            };

            function queryParamsSet(params) {
                params.search_text = $('#search_text').val();
                return JSON.stringify(params)
            }

            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('amortize_code', '项目编号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('amortize_name', '项目名称'),
                    // common.BTRowFormatWithFormatterAlignRight('amortize_budget', '预算总金额',common.priceFormat),
                    // common.BTRowFormatAlignLeft('amortize_manager', '管理责任人'),
                    // common.BTRowFormat('amortize_agelimit', '预计使用年限'),
                    // common.BTRowFormatEdSelect2Disabled(_self, 'amortize_way', '摊销方法', 'amortizedInfo'),
                    common.BTRowFormatAlignLeft('amortize_creator', '创建人'),
                    common.BTRowFormat('created_at', '创建日期'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'amortize_project_state', '项目状态', 'projectState'),
                    common.BTRowFormatAlignLeft('amortize_examine', '审批人'),
                    common.BTRowFormat('amortize_examine_time', '审批时间'),
                    // common.BTRowFormat('amortize_refuse_remark', '驳回说明'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'amortize_check_state', '验收状态', 'checkState'),
                    common.BTRowFormatAlignLeft('amortize_acceptor', '验收人'),
                    common.BTRowFormat('amortize_acceptor_time', '验收时间'),
                    // common.BTRowFormat('amortize_check_refuse_remark', '驳回说明'),
                    // common.BTRowFormat('amortize_remark', '项目描述'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'amortized_id',
                    uniqueId: 'amortized_id',
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
                        console.log(row.amortize_project_state, 'amortize_project_state')
                        if (row.amortize_project_state === 3) {
                            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'amortized_id', $('#setTable'))
                        } else if (row.amortize_project_state === 1) {
                            common.dealPromptCommon('该项目待审核，不能编辑')
                            $('#setTable').bootstrapTable("refresh")
                            return
                        } else {
                            common.dealPromptCommon('该项目已通过，不能编辑')
                            $('#setTable').bootstrapTable("refresh")
                            return
                        }
                    }

                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                common.initDatepicker($('#start_date'));
                common.initDatepicker($('#end_date'));
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                // 弹框中select选项框
                // common.initSelect2($('#amortize_departmant_id'), retData.departmentInfo);
                // common.initSelect2($('#show_amortize_departmant_id'), retData.departmentInfo);
                common.initSelect2($('#amortize_way'), retData.amortizedInfo);
                common.initSelect2($('#show_amortize_way'), retData.amortizedInfo);
                // 责任人和归属部门联动
                // common.initSelect2($('#amortize_manager'), retData.getManagerInfo);
                // $('#amortize_departmant_id').on('select2:select', async function(evt) {
                //     let departmentId = common.getSelect2Val('amortize_departmant_id')
                //     let response = await _self.$http.post(apiUrl + 'getManagerInfo', {departmant_id:departmentId});
                //     let retData = response.data.info;
                //     common.initSelect2($('#amortize_manager'), retData.marangerInfo);
                // })
                initsetTable();
                $('#formC').parsley()
            }

            $(function () {
                initData();
            })
        },
        methods: {
            queryConfirm: function (event) {
                $('#setTable').bootstrapTable("refresh");
            },

            addM: function (event) {
                let _self = this;
                _self.workRow = {};
                _self.saveId = '';
                $('#amortize_departmant_id').val(null).trigger('change');
                $('#amortize_manager').val(null).trigger('change');
                $('#amortize_way').val(null).trigger('change');
                $('#amortize_name').val('');
                $('#amortize_agelimit').val('');
                $('#AddModal').modal('show');
            },
            saveSaleOrder: async function (event) {
                let _self = this;
                let formC = $('#formC');
                if (formC.parsley().isValid()) {
                    try {

                        let params = {
                            amortize_departmant_id: _self.pagePara3.userDepartmentId,
                            amortize_manager: _self.pagePara2.meetingId,
                            amortize_remark: _self.workRow.amortize_remark,
                            amortize_way: common.getSelect2Val('amortize_way'),
                            amortize_name: _self.workRow.amortize_name,
                            amortize_agelimit: _self.workRow.amortize_agelimit
                        };
                        if (_self.saveId) {
                            params.amortize_id = _self.saveId;
                            let response = await _self.$http.post(apiUrl + 'modify', params);
                            $('#AddModal').modal('hide');

                        } else {
                            let response = await _self.$http.post(apiUrl + 'add', params);
                            $('#AddModal').modal('hide');
                        }
                        $('#setTable').bootstrapTable("refresh");
                        formC.parsley().reset();
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            saveSet: async function (event) {
                let _self = this;
                if (_self.workRow.amortize_project_state !== 3) {
                    // common.dealPromptCommon('该项目不是拒绝状态，不能对其进行修改')
                    $('#ShowModal').modal('hide');
                    return
                } else {
                    let formA = $('#formA');
                    if (formA.parsley().isValid()) {
                        try {

                            let params = {
                                amortize_departmant_id: _self.pagePara3.userDepartmentId,
                                amortize_departmant_name: _self.workRow.amortize_departmant_name,
                                amortize_manager: _self.pagePara2.meetingId,
                                amortize_remark: _self.workRow.amortize_remark,
                                amortize_way: common.getSelect2Val('show_amortize_way'),
                                amortize_name: _self.workRow.amortize_name,
                                amortize_agelimit: _self.workRow.amortize_agelimit
                            };
                            // if(_self.saveId){
                            params.amortize_id = _self.saveId;
                            let response = await _self.$http.post(apiUrl + 'modify', params);
                            // $('#AddModal').modal('hide');

                            // }else{
                            //     let response = await _self.$http.post(apiUrl + 'add', params);
                            //     $('#AddModal').modal('hide');
                            // }
                            $('#setTable').bootstrapTable("refresh");
                            // formA.parsley().reset();
                        } catch (error) {
                            common.dealErrorCommon(_self, error);
                        }
                    }
                }
            },
            submitAct: async function (event) {
                try {
                    let _self = this;
                    let params = {
                        amortize_name: _self.workRow.amortize_name,
                        amortize_id: _self.saveId
                    };
                    let response = await _self.$http.post(apiUrl + 'sendAmortizeTask', params);

                    $('#setTable').bootstrapTable("refresh");
                    $('#AddModal').modal('hide');
                    $('#ShowModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            teamP: function (index) {
                let _self = this;
                _self.index = index;
                if (_self.index == 1) {
                    _self.eventHub.$emit('show-departmentselect-dialog');
                } else if (_self.index == 2) {
                    _self.eventHub.$emit('show-userselect-dialog');
                }
            },
            departmentSelectCallback: async function (users) {
                let _self = this;
                try {
                    _self.$http.post(apiUrl + 'changeDepartment', {
                        user_id: _self.pagePara.userInfo.user_id,
                        users: users
                    }).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                        $("#amortize_departmant_id").val(_self.pagePara3.userDepartmentName)
                        _self.amortize_departmant_id = _self.pagePara3.userDepartmentId
                        $("#show_amortize_departmant_id").val(_self.pagePara3.userDepartmentName)
                        _self.show_amortize_departmant_id = _self.pagePara3.userDepartmentId
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            userSelectCallback: async function (users) {
                let _self = this;
                let params = {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }
                try {
                    _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                        $("#amortize_manager").val(_self.pagePara2.meetingName)
                        _self.amortize_manager = _self.pagePara2.meetingId
                        $("#show_amortize_manager").val(_self.pagePara2.meetingName)
                        _self.show_amortize_manager = _self.pagePara2.meetingId
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
</script>
<style scoped>
    textarea {
        width: 100%;
        resize: none
    }
</style>
