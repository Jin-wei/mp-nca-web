<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">系统管理</a></li>
            <li class="active">系统数据字典初始化列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand">
                                <i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">系统数据字典初始化列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_system" placeholder="搜索数据编码、数据名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="systemDataTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddSystemDataModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">增加数据类型</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formSD">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>数据名称</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="basetype_name" data-parsley-required="true" ></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6" style="padding-left: 0px">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>顺序编码</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.typedetail_no" data-parsley-required="true" type="number" maxlength="10" data-parsley-maxlength="10">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>数据类型</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.typedetail_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
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
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCASystemDataInitializationControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            workRow:{},
            oldRow: ''
        }
    },
    name: 'NCASystemDataInitializationControl',
    mounted: function() {
        let _self = this;
        let $systemDataTable = $('#systemDataTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 delete_meeting">删除</a>'
            ].join('')

        }

        window.tableEvents = {
            'click .delete_meeting': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $systemDataTable, row, 'basetypedetail_id', function() {})
            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text_system').val();
            return JSON.stringify(params)
        }
        function initTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('basetype_code', '数据编码'),
                common.BTRowFormatAlignLeft('basetype_name', '数据名称'),
                common.BTRowFormatEditable('typedetail_no', '顺序编码'),
                common.BTRowFormat('typedetail_code', '数据类型编码'),
                common.BTRowFormatEditable('typedetail_name', '数据类型'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $systemDataTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'basetypedetail_id',
                uniqueId: 'basetypedetail_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'basetypedetail_id', $systemDataTable)
                }
            });
            common.changeTableClass($systemDataTable)
        }

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));

            common.initSelect2($('#basetype_name'), retData.baseTypeName);

            initTable();
            common.reSizeCall();
            $('#formSD').parsley()
        }

        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#systemDataTable').bootstrapTable("refresh");
        },
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#basetype_name').val(null).trigger('change');
            $('#AddSystemDataModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formSD').parsley().isValid()) {
                try {
                    _self.workRow.basetype_code = common.getSelect2Val('basetype_name');

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    $('#systemDataTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formSD').parsley().reset();
                    $('#AddSystemDataModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
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
