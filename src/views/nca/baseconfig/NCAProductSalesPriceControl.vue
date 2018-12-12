<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">企业客户管理</a></li>
            <li class="active">销售价格模板列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">销售价格模板列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text_sales" placeholder="搜索模板名称" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="addPSP">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="productSalesPriceTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddproductSalesPriceModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增价格模板</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formPSP">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label"><span class="table-required">*</span>价格模板名称</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="workRow.producepricetemplate_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
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
const apiUrl = '/api/nca/baseconfig/NCAProductSalesPriceControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            workRow:{}
        }
    },
    name: 'NCAProductSalesPriceControl',
    mounted: function() {
        let _self = this;
        let $productSalesPriceTable = $('#productSalesPriceTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>',
                '<a class="btn btn-info btn-xs m-r-5 delete_detail">删除</a>'
            ].join('')

        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAProductSalesPriceDetailControl',
                    query: row
                })

            },
            'click .delete_detail': function(e, value, row, index) {
                common.rowDeleteWithApi(_self, '确认删除吗？', apiUrl + 'delete_t', $productSalesPriceTable, row, 'producepricetemplate_id')
            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text_sales').val();
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEditableLeft('producepricetemplate_name', '价格模板名称'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $productSalesPriceTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_t',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'producepricetemplate_id',
                uniqueId: 'producepricetemplate_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify_t', row, 'producepricetemplate_id', $productSalesPriceTable)
                }
            });
            common.changeTableClass($productSalesPriceTable)
        }

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));

            initFitOrderTable();
            common.reSizeCall();
            $('#formPSP').parsley()
        }


        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#productSalesPriceTable').bootstrapTable("refresh")
        },
        addPSP: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#AddproductSalesPriceModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formPSP').parsley().isValid()) {
                try {

                    let response = await _self.$http.post(apiUrl + 'add_t', _self.workRow);
                    $('#productSalesPriceTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formPSP').parsley().reset();
                    $('#AddproductSalesPriceModal').modal('hide');
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
