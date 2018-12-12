<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">待摊资产项目管理</a></li>
            <li class="active">资产立项</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">资产立项</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索待摊资产项目编号、待摊资产项目名称" style="width: 260px;">
                                </div>
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
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
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>预算总金额</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_amortize_budget" data-parsley-group="block-1" v-model="workRow.amortize_budget" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label" style="padding-left:0px"><span class="table-required">*</span>预计使用年限</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_amortize_agelimit" data-parsley-group="block-1" v-model="workRow.amortize_agelimit" maxlength="3" data-parsley-maxlength="3" data-parsley-required="true" data-parsley-type="number" disabled>
                                    </div>
                                </div>
                            </div>

                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门</label>
                                    <div class="col-md-9">
                                        <input class="form-control" id="show_amortize_departmant_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(1)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-9" >
                                        <input class="form-control" id="show_amortize_manager" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-md-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>
                                    </div>
                                </div>
                            </div>
                            <div class="row"  style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>推销方法</label>
                                    <div class="col-md-10">
                                        <select class="form-control select2" id="show_amortize_way" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row"  style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="amortize_refuse_remark" v-model="workRow.amortize_refuse_remark" maxlength="100" data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-top: 10px" >
                                <div class="form-group">
                                    <label class="col-md-2 control-label">项目描述</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="show_amortize_remark" v-model="workRow.amortize_remark" maxlength="100" data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row"  style="margin-top: 10px">
                                <div class="form-group">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>验收驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control select2" id="amortize_check_refuse_remark" v-model="workRow.amortize_check_refuse_remark" maxlength="100" data-parsley-maxlength="100" disabled>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <!--<button v-show="_self.saveId !== ''" type="submit" class="btn btn-info" id="submitAct" v-on:click="submitAct">再次提交</button>-->
                            <!--<button v-if="_self.saveId === ''" type="submit" class="btn btn-info" id="saveAct">提交</button>-->
                            <button type="submit" class="btn btn-info" id="Act">确认</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>


        <div class="tab-content" style="padding: 15px">
            <div class="tab-pane fade active in" id="set_table">
                <table id="setTable"></table>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCAAmortizeControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    export default {
        data: function() {
            return {
                pagePara:{},
                tableType:1,
                QRHtml:'',
                workRow:{},
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                searchedRow:[],
                saveId:'',
                show_return: 0,
                amortize_id:'',
            }
        },
        name: 'NCAAmortizeControl',
        components: {
            DomainChooseHeader
        },
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');
            _self.amortize_id = _self.$route.query.amortize_id;
            console.log(_self.amortize_id,'_self.amortize_id')

            function operateFormatter(value, row, index) {
                return [
                    // '<a class="btn btn-xs btn-info m-r-5 tableDelete" style="cursor: pointer;">删除</a>',
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
                'click .show_detail': function(e, value, row, index) {

                    if(row.amortize_project_state === 2){
                        _self.$router.push({
                            path: '/nca/longtermassets/NCAAmortizeDetailControlCopy',
                            query: {
                                amortize_id: row.amortize_id
                            }
                        })
                    }else {
                        return common.dealPromptCommon('项目未通过，不能显示详情')
                    }
                },
                'click .table_edit': async function(e, value, row, index) {

                    $('#ShowModal').modal('show');
                    _self.saveId = row.amortize_id;
                    _self.workRow = $.extend(true, {}, row)
                    let departmentId = common.getSelect2Val('amortize_departmant_id')
                    let response = await _self.$http.post(apiUrl + 'getManagerInfo', {departmant_id:departmentId});
                    let retData = response.data.info;
                    let amortizeId = common.getSelect2Val('amortize_manager')
                    common.initSelect2($('#amortize_manager'), retData.marangerInfo);
                    let amortize_way = common.getSelect2Val('amortize_way')
                    common.initSelect2($('#amortize_way'), retData.amortizedInfo);

                    row.amortize_agelimit = _self.workRow.amortize_agelimit
                    $('#show_amortize_budget').val(_self.workRow.amortize_budget).trigger('change');
                    $('#show_amortize_remark').val(_self.workRow.amortize_remark).trigger('change');
                    $('#show_amortize_departmant_id').val(_self.workRow.amortize_departmant_id).trigger('change');
                    $('#show_amortize_manager').val(_self.workRow.amortize_manager).trigger('change');
                    $('#show_amortize_way').val(_self.workRow.amortize_way).trigger('change');
                    $('#amortize_refuse_remark').val(_self.workRow.amortize_refuse_remark).trigger('change');
                    $('#show_amortize_agelimit').val(_self.workRow.amortize_agelimit).trigger('change');
                    $('#amortize_check_refuse_remark').val(_self.workRow.amortize_check_refuse_remark).trigger('change');
                },
            };

            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                params.amortize_id = _self.amortize_id;
                return JSON.stringify(params)
            }
            function initsetTable() {
                console.log(_self.pagePara.amortizedInfo)
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('amortize_code', '项目编号', nameFormatter,tableEvents),
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
                    common.BTRowFormatEdSelect2Disabled(_self,'amortize_check_state', '验收状态','checkState'),
                    common.BTRowFormatAlignLeft('amortize_acceptor', '验收人'),
                    common.BTRowFormat('amortize_acceptor_time', '验收时间'),
                    // common.BTRowFormat('amortize_check_refuse_remark', '驳回说明'),
                    // common.BTRowFormat('amortize_remark', '项目描述'),
                    common.BTRowFormatWithFE('', '操作',operateFormatter,tableEvents)
                ];
                $setTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParamsSet,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
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

                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                common.initDatepicker($('#start_date'));
                common.initDatepicker($('#end_date'));
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
                // 弹框中select选项框
                common.initSelect2($('#amortize_departmant_id'), retData.departmentInfo);
                common.initSelect2($('#amortize_way'), retData.amortizedInfo);
                // 责任人和归属部门联动
                common.initSelect2($('#amortize_manager'), retData.getManagerInfo);
                $('#amortize_departmant_id').on('select2:select', async function(evt) {
                    let departmentId = common.getSelect2Val('amortize_departmant_id')
                    let response = await _self.$http.post(apiUrl + 'getManagerInfo', {departmant_id:departmentId});
                    let retData = response.data.info;
                    common.initSelect2($('#amortize_manager'), retData.marangerInfo);
                })
                initsetTable();
                $('#formC').parsley()
            }
            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                $('#setTable').bootstrapTable("refresh");
            },
            saveSet: async function(event){
                $('#ShowModal').modal('hide');
            },
        }
    }
</script>
<style scoped>
</style>
