<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">运营数据管理</a></li>
        <li class="active">签约工长列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">签约工长列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索用户名、姓名、电话" style="width: 200px;">
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
                    <table id="foremanTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增签约工长</h4>
                </div>
                <form @submit.prevent="addForeman" id="formA">
                    <div class="modal-body">
                        <div class="form-group">
                            <label><span class="table-required">*</span>工人搜索</label>
                            <select class="form-control select2" id="foreman_keyword" data-parsley-required="true"></select>
                        </div>

                        <div class="form-group">
                            <label><span class="table-required">*</span>签约日期</label>
                            <input type="text" class="form-control" id="sign_date" data-parsley-required="true"/>
                        </div>

                        <div class="form-group">
                            <label><span class="table-required">*</span>保证金</label>
                            <input class="form-control" v-model="bindData.domainsign_deposit" data-parsley-required="true" data-parsley-type="number">
                        </div>
                        <div class="form-group">
                            <label>备注</label>
                            <input class="form-control" v-model="bindData.domainsign_remark" data-parsley-required="false">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <div class="modal fade" id="editModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">修改签约日期</h4>
                </div>
                <form @submit.prevent="editSignDate" id="formB">
                    <div class="modal-body">
                        <div class="form-group">
                            <label>签约日期</label>
                            <input type="text" class="form-control" id="sign_date2" v-model="rowData.domainsign_date" data-parsley-required="true"/>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">修改</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCAForemanControl?method='

export default {
    data: function() {
        return {
            pagePara: {},
            rowData: {},
            oldRow: {},
            bindData: {},
            search_text: null,
            masterTable: null,
            signDate: null,
            signDate2: null
        }
    },
    name: 'foremanControl',
    filters: {
        date: function (value) {
            return new Date(value).toLocaleDateString();
        }
    },
    mounted: function() {
        this.masterTable = $('#foremanTable');
        this.signDate = $('#sign_date');
        this.signDate2 = $('#sign_date2');
        this.initPage();
    },
    methods: {
        queryParams: function (params) {
            params.search_text = this.search_text;
            return JSON.stringify(params)
        },
        initTable: function () {
            window.tableEvents = {
                'click .tableEdit': (e, value, row, index) => {
                    this.rowData = $.extend(true, {}, row);
                    let date = new Date(this.rowData.domainsign_date);
                    this.rowData.domainsign_date = `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()}`;
                    $('#editModal').modal('show');
                },
                'click .tableDelete': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '签约工长删除', apiUrl + 'delete', this.masterTable, row, 'domainsignworker_id')
                }
            }
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchDomainForeman',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('username', '用户名'),
                    common.BTRowFormatAlignLeft('name', '姓名'),
                    common.BTRowFormatEvent('domainsign_date', '签约日期', value => {
                        let date = new Date(value);
                        return `<a class="btn btn-xs tableEdit">${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()}</a>`;
                    }, tableEvents),
                    common.BTRowFormatEditableRight('domainsign_deposit', '保证金',common.priceFormat),
                    common.BTRowFormatEdTextareaWidthAlignLeft('domainsign_remark', '备注',150,10),
                    common.actFormatter('act',
                        `<a class="btn btn-xs btn-info tableDelete">删除</a>`,
                        tableEvents)
                ],
                idField: 'user_id',
                uniqueId: 'user_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modify', row, 'user_id', this.masterTable)
                }
            })
            common.changeTableClass(this.masterTable)
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.initTable()
                $('#formA').parsley()
                common.reSizeCall()
                console.log('init success')
            }, (response) => {
                console.log('init error')
                common.dealErrorCommon(this, response)
            })

            common.initDatepicker(this.signDate);
            common.initDatepicker(this.signDate2);
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams)
        },
        addM: function(event) {
            this.rowData = {}
            this.$http.post(apiUrl + 'search').then(response => {
                let retData = response.data.info;
                let listData = retData.rows.map((item, index) => ({
                    id: item.user_id,
                    text: item.name
                }));
                common.initSelect2($('#foreman_keyword'), listData)
                $('#AddModal').modal('show')
            });
        },
        addForeman: function(event) {
            if ($('#formA').parsley().isValid()) {
                if (this.signDate.val()) {
                    this.bindData.domainsign_date = this.signDate.val()
                }
                this.bindData.user_id = common.getSelect2Val('foreman_keyword')
                console.log(this.bindData)
                this.$http.post(apiUrl + 'add', this.bindData).then(response => {
                    this.masterTable.bootstrapTable('refresh')
                    this.rowData = {}
                    $('#formA').parsley().reset()
                    $('#AddModal').modal('hide')
                    common.dealSuccessCommon('增加成功')
                }, (response) => {
                    console.log('add error')
                    common.dealErrorCommon(this, response)
                })
            }
        },
        editSignDate: function () {
            let editData = {
                user_id: this.rowData.user_id,
                domainsign_date: $('#sign_date2').val()
            }
            console.log('editSignDate:', editData);
            this.$http.post(apiUrl + 'editSignDate', editData).then(response => {
                this.masterTable.bootstrapTable('refresh')
                this.rowData = {}
                $('#editModal').modal('hide');
                common.dealSuccessCommon('修改成功')
            }, (response) => {
                common.dealErrorCommon(this, response)
            })
        }
    }
}
</script>
<style scoped>
</style>
