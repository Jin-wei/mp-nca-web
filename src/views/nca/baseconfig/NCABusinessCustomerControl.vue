<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">B端客户管理</a></li>
            <li class="active">体验店列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">体验店列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索客户编号，客户名称" style="width: 200px;">
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

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增体验店列表</h4>
                    </div>
                    <form @submit.prevent="addDm" id="formA">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户编号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.domain" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户名称</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.domain_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">适用税率</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.business_tax">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">工商登记号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.businessregistration">
                                </div>
                            </div>
                            <div class="row  m_r" style="padding-bottom: 15px">
                                <div data-toggle="distpicker" id="distpicker">
                                    <div class="col-sm-4 p_l" style="margin-left: 34px">
                                        <span class="col-sm-3 control-label font-style"><span class="table-required">*</span>省</span>
                                        <div class="col-sm-9">
                                            <select class="form-control picker-width" id="domain_province" data-parsley-required="true"></select>
                                        </div>
                                    </div>
                                    <div class="col-sm-4 m_l_5">
                                        <span class="col-sm-4 control-label font-style m_l"><span class="table-required">*</span>市/县</span>
                                        <div class="col-sm-9">
                                            <select class="form-control picker-width" id="domain_city" data-parsley-required="true"></select>
                                        </div>
                                    </div>
                                    <div class="col-sm-4 m_l_41">
                                        <span class="col-sm-3 control-label font-style"><span class="table-required">*</span>区</span>
                                        <div class="col-sm-9">
                                            <select class="form-control picker-width" id="domain_district" data-parsley-required="true"></select>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-12 m_l_4">
                                <label class="col-sm-2 control-label"><span class="table-required">*</span>客户地址</label>
                                <div class="col-sm-10">
                                    <input class="form-control" v-model="workRow.domain_address" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label "><span class="table-required">*</span>联系人</label>
                                <div class="col-sm-8">
                                    <input class="form-control " v-model="workRow.domain_contact" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>联系方式</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.domain_phone" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>机构类型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="domainType" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户类型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="businesscustomer_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">传真</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.domain_fax">
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
    const apiUrl = '/api/nca/baseconfig/NCABusinessCustomerControl?method=';

    function queryParams(params) {
        params.search_text = $('#search_text').val();
        return JSON.stringify(params)
    }
    export default {
        data: function() {
            return {
                pagePara: '',
                workRow: {},
                oldRow: '',
            }
        },
        name: 'NCABusinessCustomerControl',
        mounted: function() {
            let _self = this
            let $table = $('#table')

            function actFormatter(value, row) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 edit_detail">查看</a>',
                ].join('')
            }

            window.tableEvents = {
                'click .edit_detail': function (e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCABusinessCustomerDetailControl',
                        query: {
                            domain_id: row.domain_id,
                            domain: row.domain,
                            domain_name: row.domain_name,
                            domain_address:row.domain_address,
                            domain_phone: row.domain_phone,
                            domain_contact: row.domain_contact,
                            businesscustomer_type: row.businesscustomer_type,
                            businessdomain_id: row.businessdomain_id
                        }
                    })

                }
            }
            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('domain', '客户编号'),
                        common.BTRowFormatAlignLeft('domain_name', '客户名称'),
                        common.BTRowFormatEdSelect2(_self, 'businesscustomer_type', '客户类别', 'businesscustomerType'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'domain_type', '机构类型', 'domainTypeInfo'),
                        common.BTRowFormatWithFormatterAlignLeft('domain_address', '客户地址',common.remarkFormatter),
                        common.BTRowFormatAlignLeft('domain_contact', '联系人'),
                        common.BTRowFormat('domain_phone', '联系方式'),
                        common.BTRowFormatEditable('domain_fax', '传真'),
                        common.BTRowFormatEditable('businessregistration', '工商登记号'),
                        common.BTRowFormatEditableRight('business_tax', '适用税率'),
                        common.actFormatter('act', actFormatter, tableEvents),
                    ],
                    idField: 'domain_id',
                    uniqueId: 'domain_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'domain_id', $table)
                        window.setTimeout(function () {
                            $('#table').bootstrapTable("refresh");
                        },500);
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($table)
            }

            async function initData() {
                await _self.getPagePara()
                common.initSelect2($('#businesscustomer_type'), _self.pagePara.businesscustomerType)
                common.initSelect2($('#domainType'), _self.pagePara.companyTypeInfo)
                initTable();
                common.reSizeCall();
            }
            initData()

        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            addM: function(event) {
                let _self = this
                _self.workRow = {}
                $('#distpicker').distpicker('reset', true);
                $('#formA').parsley().reset()
                $('#domainType').val(null).trigger('change')
                $('#businesscustomer_type').val(null).trigger('change')
                $('#AddModal').modal('show')
            },
            search: function(event) {
                $('#table').bootstrapTable("refresh")
            },
            addDm: async function(event) {
                let _self = this
                try {
                    if ($('#formA').parsley().isValid()) {
                        _self.workRow.domaintemplate_id = 2
                        _self.workRow.businesscustomer_type = common.getSelect2Val('businesscustomer_type')
                        _self.workRow.domain_province = $('#domain_province').val();
                        _self.workRow.domain_city = $('#domain_city').val();
                        _self.workRow.domain_district = $('#domain_district').val();
                        _self.workRow.domain_type = common.getSelect2Val('domainType')
                        let response = await _self.$http.post(apiUrl + 'add', _self.workRow)
                        let retData = response.data.info
                        $('#table').bootstrapTable("refresh");
                        common.dealSuccessCommon('增加成功')
                        $('#AddModal').modal('hide')
                        console.log('add success')
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
        }
    }
</script>
<style scoped>
    #search_div input {
        width: 200px;
    }

    #distpicker {
        padding-right: 20px;
    }

    .font-style{
        font-family: 'Open Sans','Helvetica Neue',Helvetica,Arial,sans-serif;
        font-size: 12px;
        color: #242a30;
        font-weight: 600;
        display: inline-block;
        max-width: 100%;
    }

    .m_l{
        margin-left: -21px;
    }

    .m_l_41{
        margin-left: -41px;
    }

    .m_l_5{
        margin-left: 5px;
    }

    .m_r{
        margin-right: -27px;
    }

    .p_l{
         padding-left: 36px;
         padding-right: 0px;
     }

    .m_l_4{
        margin-left: -4px;
        width: 594px;
    }
</style>
