<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">资产验收管理</a></li>
        <li class="active">资产验收单详情</li>
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
                    <h4 class="panel-title">资产验收单详情</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control"  id="search_text_AADC" placeholder="搜索固定资产编号、固定资产名称" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body">
                    <div class="tab-content">
                        <div class="tab-pane fade active in">
                            <table id="assetsAcceptanceTaskDetailTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AssetsAcceptanceModal">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">查看固定资产验收项记录</h4>
                </div>
                <form @submit.prevent="confirmForm" id="formA">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label">预计使用年限</label>
                                <div class="col-sm-10">
                                    <input class="form-control" v-model="workRow.use_time_limit" id="show_use_time_limit" maxlength="7" data-parsley-maxlength="7" data-parsley-type="number" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label">预计净残值率</label>
                                <div class="col-sm-10 p_r">
                                    <input class="form-control" v-model="workRow.residual_value_rate" id="show_residual_value_rate" maxlength="7" data-parsley-maxlength="7" disabled>
                                    <span class="p_a">%</span>
                                </div>
                            </div>
                        </div>
                        <!--<div class="row">-->
                        <!--<div class="form-group col-sm-12">-->
                        <!--<label class="col-sm-2 control-label"><span class="table-required">*</span>折旧方法</label>-->
                        <!--<div class="col-sm-10">-->
                        <!--<select class="form-control select2" id="show_epreciation_category" data-parsley-required="true" data-parsley-group="block-1"></select>-->
                        <!--</div>-->
                        <!--</div>-->
                        <!--</div>-->
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门编号</label>
                                <div class="col-md-10">
                                    <input class="form-control" id="show_department_id" data-parsley-required="true" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>归属部门名称</label>
                                <div class="col-md-10">
                                    <input class="form-control" id="department_id_show" data-parsley-required="true" disabled>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="width: 900px;padding-left: 19px">
                                    <label class="col-md-2 control-label"><span class="table-required">*</span>管理责任人</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="show_user_id" data-parsley-required="true" disabled>
                                    </div>
                                    <!--<div class="col-md-1 padding-top-7">-->
                                    <!--<label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP(2)">选择</label>-->
                                    <!--</div>-->
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 18px">
                                    <label class="col-sm-2 control-label">附件</label>
                                    <div class="col-sm-10">
                                        <input id="fileU" type="file" style="margin-top: 10px" >
                                        <div style="margin-top: 10px">
                                            <p  v-for="(file,index) in detail.files" style="font-size:16px" >
                                                <a :href=file.file_url target="_blank">
                                                    <i class="glyphicon glyphicon-file"  style="font-size:22px"></i>
                                                    {{file.file_name}}
                                                </a>
                                                <i class="glyphicon glyphicon-remove-sign delete_icon" v-on:click="removeImage(index,file)" ></i>
                                            </p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info" id="save">确认</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/longtermassets/NCAFixedAssetsControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            pagePara:{},
            fixedassetscheck_id:'',
            fixedassetscheck_no: '',
            show_return: 0,
            detail:{
                files:[]
            },
        }
    },
    name: 'NCAAssetsAcceptanceTaskDetailControl',
    mounted: function() {
        let _self = this;
        let $assetsAcceptanceTaskDetailTable = $('#assetsAcceptanceTaskDetailTable');

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            _self.pagePara = JSON.parse(JSON.stringify(response.data.info));
            common.initSelect2($('#fixedassets_category'), _self.pagePara.fixedassetstype);
            common.initSelect2($('#depreciation_category'), _self.pagePara.depreciationmethodtype);
            common.initSelect2($('#fixedassetscheck_acceptance'), _self.pagePara.acceptancetype);
            _self.fixedassetscheck_id = common.getUrlParams('fixedassetscheck_id');
            _self.fixedassetscheck_no = common.getUrlParams('fixedassetscheck_no');
            _self.show_return = _self.$route.query.show_return;
            console.log('show_return',_self.show_return)

            initAllDemandTable();
            common.reSizeCall();
        }
        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_meeting">查看</a>'
            ].join('')
        }
        function queryParams(params) {
            params.search_text=$('#search_text_AADC').val();
            params.fixedassetscheck_id= _self.fixedassetscheck_id;
            return JSON.stringify(params)
        }
        function initAllDemandTable() {
            window.tableEvents = {
                'click .show_meeting': function (e,value,row,index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)

                    $('#show_use_time_limit').val(_self.workRow.use_time_limit).trigger('change');
                    $('#show_residual_value_rate').val(_self.workRow.residual_value_rate).trigger('change');
                    $('#show_department_id').val(_self.workRow.department_id).trigger('change');
                    $('#department_id_show').val(_self.workRow.department_name).trigger('change');
                    $('#fileU').val(_self.workRow.files).trigger('change');
                    $('#show_user_id').val(_self.workRow.user_name).trigger('change');
                    _self.user_id = row.user_id;
                    _self.department_id2 = row.department_id;
                    _self.detail=row;
                    $('#AssetsAcceptanceModal').modal("show")
                }
            };
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('fixedassets_no', '固定资产编号'),
                common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                common.BTRowFormat('fixedassets_model', '规格型号'),
                common.BTRowFormat('fixedassets_unit', '计量单位'),
                common.BTRowFormatEdSelect2Disabled(_self, 'fixedassets_category', '固定资产分类', 'fixedassetstype'),
                // common.BTRowFormatAlignRight('use_time_limit', '预计使用年限'),
                // common.BTRowFormatAlignRight('residual_value_rate', '预计净残值率'),
                common.BTRowFormatEdSelect2Disabled(_self, 'depreciation_category', '折旧方法', 'depreciationmethodtype'),
                // common.BTRowFormat('department_id', '归属部门编号'),
                // common.BTRowFormatAlignLeft('department_name', '归属部门名称'),
                common.BTRowFormat('created_at', '购入验收时间'),
                // common.BTRowFormatWithFE('files', '附件', common.filesFormatter),
                // common.BTRowFormatAlignLeft('user_name', '管理责任人'),
                common.BTRowFormatEdSelect2Disabled(_self, 'fixedassetscheck_acceptance', '验收类型', 'acceptancetype'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];

            $assetsAcceptanceTaskDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_cd',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'fixedassetscheckdetail_id',
                uniqueId: 'fixedassetscheckdetail_id',
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
                onEditableSave: function(field, row, oldValue, $el) {
                    // common.rowModifyWithT(_self, apiUrl + 'modify_doc', row, 'fixedassetscheckdetail_id', $assetsAcceptanceTaskDetailTable)
                }
            });
            common.changeTableClass($assetsAcceptanceTaskDetailTable)
        }

        $(function() {
            initData();

            common.fileUpload(_self, $('#file'), apiUrl, function(fileInfo) {
                let convert = {} //转换字段名
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                _self.detail.files.push(convert);
                $('#file').val('');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#assetsAcceptanceTaskDetailTable').bootstrapTable("refresh")
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
