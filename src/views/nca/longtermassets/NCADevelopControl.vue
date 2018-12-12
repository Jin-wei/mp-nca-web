<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">研发项目管理</a></li>
            <li class="active">研发项目列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">研发项目列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">

                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索研发项目编号、研发项目名称" style="width: 260px;">
                                </div>
                            </div>
                            <div class="form-group">
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
                        <div class="tab-pane fade active in" id="set_table">
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
                        <!--<h4 class="modal-title">新增研发项目记录</h4>-->
                        <h4 v-if="saveId !== ''"class="modal-title">修改研发项目记录</h4>
                        <h4 v-else class="modal-title">新增研发项目记录</h4>
                    </div>
                    <form @submit.prevent="saveSaleOrder" id="formC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>项目名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="develop_name" data-parsley-group="block-1" v-model="workRow.develop_name" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="develop_format" data-parsley-group="block-1" v-model="workRow.develop_format" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true">
                                    </div>
                                </div>
                            </div>

                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>计量单位</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="develop_unit" data-parsley-group="block-1" v-model="workRow.develop_unit" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true">
                                    </div>
                                </div>

                            </div>
                            <div class="row" style="margin-left: -14px">
                                <div class="form-group col-sm-12">
                                    <label class="col-md-2 control-label">项目描述</label>
                                    <div class="col-md-10">
                                        <input class="form-control" style="width: 448px;" id="develop_remark" data-parsley-group="block-1" v-model="workRow.develop_remark" maxlength="200" data-parsley-maxlength="200" >
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button v-show="_self.saveId !== ''" type="submit" class="btn btn-info" id="submitAct" v-on:click="submitAct">再次提交</button>
                            <button v-if="saveId === '' " type="submit" class="btn btn-info" id="saveAct">提交</button>
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
                        <!--<h4 class="modal-title">新增研发项目记录</h4>-->
                        <!--<h4 v-if="saveId !== ''"class="modal-title">修改研发项目记录</h4>-->
                        <h4 class="modal-title">查看研发项目记录</h4>
                    </div>
                    <form @submit.prevent="SaleOrder" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>项目名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="show_develop_name" data-parsley-group="block-1" v-model="workRow.develop_name" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-md-4 control-label"><span class="table-required">*</span>计量单位</label>
                                    <div class="col-md-8">
                                        <input class="form-control" id="show_develop_unit" data-parsley-group="block-1" v-model="workRow.develop_unit" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label" style="padding-left:0px"><span class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="show_develop_format" data-parsley-group="block-1" v-model="workRow.develop_format" maxlength="100" data-parsley-maxlength="100" data-parsley-required="true">
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
                                        <input class="form-control" style="width: 448px;" id="show_develop_remark" data-parsley-group="block-1" v-model="workRow.develop_remark" maxlength="200" data-parsley-maxlength="200">
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button v-show="_self.workRow.develop_project_state === 3" type="submit" class="btn btn-info" id="show_submitAct" v-on:click="submitAct">再次提交</button>
                            <!--<button v-if="_self.workRow.develop_project_state !== 3 " type="submit" class="btn btn-info" id="show_saveAct">确认</button>-->
                            <button v-show="_self.workRow.develop_project_state === 3" type="submit" class="btn btn-info" id="show_comfirmAct">修改</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>


    </div>
</template>
<script>
    import Vue from 'vue'
    const common = require('commonFunc');
    const apiUrl = '/api/nca/longtermassets/NCADevelopControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    import userNewDepartment from '../baseconfig/userNewDepartment.vue';
    import userSelectDialog from '../../../components/common/userSelectDialog.vue'
    export default {
        data: function() {
            return {
                pagePara:{},
                pagePara2:{},
                pagePara3:{},
                tableType:1,
                QRHtml:'',
                workRow:{},
                apiName: common.getApiName(apiUrl),
                userinfo: common.getStoreData('userinfo'),
                searchedRow:[],
                saveId:'',
                usergroupId: '',
                users: [],
                eventHub: new Vue(),
                index: '',
                mulityFlag: '',
            }
        },
        name: 'NCADevelopControl',
        components: {
            DomainChooseHeader,
            userNewDepartment,
            userSelectDialog
        },
        mounted: function() {
            let _self = this;
            let $setTable = $('#setTable');

            function operateFormatter(value, row, index) {
                return [
                    '<a class="tableDelete btn btn-info btn-xs m-r-5" style="cursor: pointer;">删除</a>',
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
                            path: '/nca/longtermassets/NCADevelopDetailControl',
                            query: {
                                develop_id: row.develop_id
                            }
                        })
                    }else {
                        return common.dealPromptCommon('项目未通过，不能显示详情')
                    }
                },
                'click .tableDelete': function(e, value, row, index) {
                    if(row.develop_project_state === 3){
                        common.dealConfrimCommon('您确定删除么', function() {
                            _self.$http.post(apiUrl + 'delete', {develop_id: row.develop_id}).then((response) => {
                                $setTable.bootstrapTable("refresh");
                            }, (response) => {
                            });
                        });
                    }else if (row.develop_project_state ===1) {
                        return common.dealPromptCommon('该项目待审核，不能删除')
                    }else {
                        return common.dealPromptCommon('该项目已通过，不能删除')
                    }
                },
                'click .table_edit':async function (e,value,row,index) {
                    $('#ShowModal').modal('show');
                    // console.log(6666)
                    // if(row.develop_project_state === 3){
                    //     console.log(3333)
                    //     console.log(row.develop_id,'row')
                        _self.saveId = row.develop_id;
                        _self.workRow = $.extend(true, {}, row)
                        $('#develop_budget').val(_self.workRow.develop_budget).trigger('change');
                        $('#show_develop_format').val(_self.workRow.develop_format).trigger('change');
                        $('#develop_refuse_remark').val(_self.workRow.develop_refuse_remark).trigger('change');
                        $('#develop_check_refuse_remark').val(_self.workRow.develop_check_refuse_remark).trigger('change');
                        $('#develop_remark').val(_self.workRow.develop_remark).trigger('change');
                        // $('#AddModal').modal('show');
                    // }else{
                    //     return common.dealPromptCommon('该项目不是拒绝状态，不能编辑')
                    // }

                }
            };

            function queryParamsSet(params) {
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initsetTable() {
                let columsArr = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('develop_code', '项目编号', nameFormatter,tableEvents),
                    common.BTRowFormatWithFormatterAlignLeft('develop_name', '项目名称',common.remarkFormatter),
                    // common.BTRowFormatWithFormatterAlignRight('develop_budget', '预算总金额',common.priceFormat),
                    // common.BTRowFormatWithFormatter('develop_format', '规格型号',common.remarkFormatter),
                    common.BTRowFormat('develop_unit', '计量单位'),
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
                    idField: 'develop_id',
                    uniqueId: 'develop_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: async function(field, row, oldValue, $el) {
                        if(row.develop_project_state === 3){
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'develop_id', $('#setTable'))
                    }else{
                            common.dealPromptCommon('该项目不是拒绝状态，不能编辑')
                            $('#setTable').bootstrapTable("refresh")
                            return
                        }
                    },
                    onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }

                });
                common.changeTableClass($setTable)
            }

            async function initData() {
                let response = {};
                response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                response = await _self.$http.post(apiUrl + 'getManagerInfo', {develop_id:_self.develop_id});
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

            addM: function(event) {
                let _self = this;
                _self.workRow = {};
                _self.saveId = '';
                $('#develop_name').val('');
                $('#AddModal').modal('show');
            },
            saveSaleOrder: async function(event) {
                let _self = this;
                let formC = $('#formC');
                if (formC.parsley().isValid()) {
                    try {

                        let params = {
                            develop_name:_self.workRow.develop_name,
                            develop_format: _self.workRow.develop_format,
                            develop_unit: _self.workRow.develop_unit,
                            develop_remark: _self.workRow.develop_remark,
                        };
                        // if(_self.saveId){
                        //     params.develop_id = _self.saveId;
                            // let response = await _self.$http.post(apiUrl + 'modify', params);
                            // $('#AddModal').modal('hide');

                        // }else{
                            let response = await _self.$http.post(apiUrl + 'add', params);
                            $('#AddModal').modal('hide');
                        // }
                            // let response = await _self.$http.post(apiUrl + 'add', params);
                            // $('#AddModal').modal('hide');
                        $('#setTable').bootstrapTable("refresh");
                        formC.parsley().reset();
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            },
            SaleOrder: async function() {
                let _self = this;
                console.log(_self,'self')
                if(_self.workRow.develop_project_state !== 3){
                    $('#ShowModal').modal('hide');
                    // return common.dealPromptCommon('该项目不为拒绝状态，不能进行修改')
                }else{
                    let formA = $('#formA');
                    if (formA.parsley().isValid()) {
                        try {

                            console.log(_self,'self')
                            let params = {
                                develop_name:_self.workRow.develop_name,
                                develop_format: _self.workRow.develop_format,
                                develop_unit: _self.workRow.develop_unit,
                                develop_remark: _self.workRow.develop_remark,
                            };
                            params.develop_id = _self.saveId;
                            console.log(params.develop_id,'develop_id')
                            let response = await _self.$http.post(apiUrl + 'modify', params);
                            console.log(params,'params')
                            // $('#ShowModal').modal('hide');

                            $('#setTable').bootstrapTable("refresh");
                            formA.parsley().reset();
                        } catch (error) {
                            common.dealErrorCommon(_self, error);
                        }
                    }
                }


            },
            submitAct: async function(event) {
                try {
                    let _self = this;
                    let params = {
                        develop_name:_self.workRow.develop_name,
                        develop_id: _self.saveId,
                        develop_format: _self.workRow.develop_format,
                        develop_unit: _self.workRow.develop_unit,
                        develop_remark: _self.workRow.develop_remark,
                    };
                    let response = await _self.$http.post(apiUrl + 'sendDevelopTask', params);

                    $('#setTable').bootstrapTable("refresh");
                    $('#AddModal').modal('hide');
                    $('#ShowModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            teamP: function(index) {
                let _self = this;
                _self.index = index;
                if (_self.index == 1) {
                    _self.eventHub.$emit('show-departmentselect-dialog');
                } else if (_self.index == 2) {
                    _self.eventHub.$emit('show-userselect-dialog');
                }
            },
            departmentSelectCallback: async function(users) {
                let _self = this;
                try {
                    _self.$http.post(apiUrl + 'changeDepartment', {
                        user_id: _self.pagePara.userInfo.user_id,
                        users: users
                    }).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                        $("#develop_departmant_id").val(_self.pagePara3.userDepartmentName)
                        _self.develop_departmant_id = _self.pagePara3.userDepartmentId
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            userSelectCallback: async function(users) {
                let _self = this;
                let params =  {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }
                try {
                    _self.$http.post(apiUrl + 'changeGroup', params).then((response) => {
                        let retData = response.data.info;
                        _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                        $("#develop_manager").val(_self.pagePara2.meetingName)
                        _self.develop_manager = _self.pagePara2.meetingId
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
    textarea{
        width:100%;resize:none
    }
</style>
