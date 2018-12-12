<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">B端客户管理</a></li>
            <li class="active">企业客户列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">企业客户列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索客户编号、客户姓名" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="corporate_type" style="width: 150px;">
                                        <option value="">客户类别</option>
                                        <option v-for="state in pagePara.businesscustomerType" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addCCC" class="btn btn-info" v-on:click="addCCC">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="corporateClientsTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddCorporateClientsModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增企业客户列表</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formCCC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户名称</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.corporateclients_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户类别</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="corporateclients_type" data-parsley-required="true" ></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">客户地址</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" v-model="workRow.corporateclients_address">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">联系电话</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" v-model="workRow.corporateclients_mobile_phone" data-parsley-phone="true">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">传真</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" v-model="workRow.corporateclients_fax">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">组织机构代码</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" v-model="workRow.business_registration_no">
                                    </div>
                                </div>
                            </div>
                            <!--<div class="row">-->
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-4 control-label">适用税率</label>-->
                                    <!--<div class="col-sm-7 p_r">-->
                                        <!--<input type="text" class="form-control" v-model="workRow.business_tax">-->
                                        <!--<span class="p_a">%</span>-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
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
const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            workRow:{}
        }
    },
    name: 'NCACorporateClientsControl',
    mounted: function() {
        let _self = this;
        let $corporateClientsTable = $('#corporateClientsTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>'
            ].join('')

        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCACorporateClientsDetailControl',
                    query: row
                })

            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            params.corporateclients_type= $('#corporate_type').val();
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('corporateclients_no', '客户编号'),
                common.BTRowFormatEditableLeft('corporateclients_name', '客户姓名'),
                common.BTRowFormatEdSelect2(_self, 'corporateclients_type', '客户类别', 'businesscustomerType'),
                common.BTRowFormatWithFormatterAlignLeft('corporateclients_address', '客户地址',common.remarkFormatter),
                common.BTRowFormatEditable('corporateclients_mobile_phone', '联系电话'),
                common.BTRowFormatEditable('corporateclients_fax', '传真'),
                common.BTRowFormatEditable('business_registration_no', '组织机构代码'),
                // common.BTRowFormatEditable('business_tax', '适用税率'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $corporateClientsTable.bootstrapTable({
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
                idField: 'corporateclients_id',
                uniqueId: 'corporateclients_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_doc', row, 'corporateclients_id', $corporateClientsTable)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($corporateClientsTable)
        }

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            common.initSelect2($('#corporateclients_type'), retData.businesscustomerType)

            initFitOrderTable();
            common.reSizeCall();
            $('#formCCC').parsley()
        }


        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#corporateClientsTable').bootstrapTable("refresh")
        },
        addCCC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#corporateclients_type').val(null).trigger('change');
            $('#AddCorporateClientsModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formCCC').parsley().isValid()) {
                try {
                    _self.workRow.corporateclients_type = common.getSelect2Val('corporateclients_type');

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    $('#corporateClientsTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formCCC').parsley().reset();
                    $('#AddCorporateClientsModal').modal('hide');
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
.p_r{
    position:relative;
}
.p_a{
    position:absolute;
    top:8px;
    right:0px;
}
</style>
