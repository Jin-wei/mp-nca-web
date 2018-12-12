<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">研发项目管理</a></li>
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
                                    <input class="form-control"  id="search_text" placeholder="搜索研发项目编号、研发项目名称" style="width: 260px;">
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
                        <!--<h4 class="modal-title">新增研发项目记录</h4>-->
                        <!--<h4 v-if="saveId !== ''"class="modal-title">修改研发项目记录</h4>-->
                        <h4 class="modal-title">查看研发项目记录</h4>
                    </div>
                    <form @submit.prevent="SaleOrder" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="show_develop_format" data-parsley-group="block-1" v-model="workRow.develop_format" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>预算总金额</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="develop_budget" data-parsley-group="block-1" v-model="workRow.develop_budget" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label">驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control" style="width: 448px;" id="develop_refuse_remark" data-parsley-group="block-1" v-model="workRow.develop_refuse_remark" maxlength="200" data-parsley-maxlength="200" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label">验收驳回说明</label>
                                    <div class="col-md-10">
                                        <input class="form-control" style="width: 448px;" id="develop_check_refuse_remark" data-parsley-group="block-1" v-model="workRow.develop_check_refuse_remark" maxlength="200" data-parsley-maxlength="200" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label">项目描述</label>
                                    <div class="col-md-10">
                                        <input class="form-control" style="width: 448px;" id="show_develop_remark" data-parsley-group="block-1" v-model="workRow.develop_remark" maxlength="200" data-parsley-maxlength="200" disabled>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
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
    const apiUrl = '/api/nca/longtermassets/NCADevelopControl?method=';
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
                develop_id:'',
            }
        },
        name: 'NCADevelopControl',
        components: {
            DomainChooseHeader
        },
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');
            _self.develop_id = _self.$route.query.develop_id;
            console.log(_self.develop_id,'_self.develop_id')

            function operateFormatter(value, row, index) {
                return [
                    '<a class="table_edit btn btn-info btn-xs m-r-5" style="cursor: pointer;">查看</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 table_edit btn-info-delete">编辑</a>',
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

                    if(row.develop_project_state === 2){
                        _self.$router.push({
                            path: '/nca/longtermassets/NCADevelopDetailControlCopy',
                            query: {
                                develop_id: row.develop_id
                            }
                        })
                    }else {
                        return common.dealPromptCommon('项目未通过，不能显示详情')
                    }
                },
                'click .table_edit': async function(e, value, row, index) {
                    $('#ShowModal').modal('show')
                    _self.saveId = row.develop_id;
                    _self.workRow = $.extend(true, {}, row)
                    $('#develop_budget').val(_self.workRow.develop_budget).trigger('change');
                    $('#show_develop_format').val(_self.workRow.develop_format).trigger('change');
                    $('#develop_refuse_remark').val(_self.workRow.develop_refuse_remark).trigger('change');
                    $('#develop_check_refuse_remark').val(_self.workRow.develop_check_refuse_remark).trigger('change');
                    $('#develop_remark').val(_self.workRow.develop_remark).trigger('change');
                },
            };

            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                params.develop_id = _self.develop_id;
                return JSON.stringify(params)
            }
            function initsetTable() {
                console.log(_self.pagePara.developdInfo)
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('develop_code', '项目编号', nameFormatter,tableEvents),
                    common.BTRowFormatAlignLeft('develop_name', '项目名称',common.remarkFormatter),
                    // common.BTRowFormatWithFormatterAlignRight('develop_budget', '预算总金额',common.priceFormat),
                    // common.BTRowFormatWithFormatter('develop_format', '规格型号',common.remarkFormatter),
                    common.BTRowFormat('develop_unit', '计量单位',common.remarkFormatter),
                    common.BTRowFormatAlignLeft('develop_creator', '创建人'),
                    common.BTRowFormat('created_at', '创建日期'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'develop_project_state', '项目状态', 'projectState'),
                    common.BTRowFormatAlignLeft('develop_examine', '审批人'),
                    common.BTRowFormat('develop_examine_time', '审批时间'),
                    // common.BTRowFormatWithFormatter('develop_refuse_remark', '驳回说明',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self,'develop_check_state', '验收状态','checkState'),
                    common.BTRowFormatAlignLeft('develop_acceptor', '验收人'),
                    // common.BTRowFormatWithFormatter('develop_check_refuse_remark', '驳回说明',common.remarkFormatter),
                    /*common.BTRowFormat('develop_remark', '项目描述'),*/
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
                    idField: 'developd_id',
                    uniqueId: 'developd_id',
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
                common.initSelect2($('#develop_departmant_id'), retData.departmentInfo);
                common.initSelect2($('#develop_way'), retData.developdInfo);
                // 责任人和归属部门联动
                common.initSelect2($('#develop_manager'), retData.getManagerInfo);
                $('#develop_departmant_id').on('select2:select', async function(evt) {
                    let departmentId = common.getSelect2Val('develop_departmant_id')
                    let response = await _self.$http.post(apiUrl + 'getManagerInfo', {departmant_id:departmentId});
                    let retData = response.data.info;
                    common.initSelect2($('#develop_manager'), retData.marangerInfo);
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
            SaleOrder: async function() {
                $('#ShowModal').modal('hide');
            },
        }
    }
</script>
<style scoped>
</style>
