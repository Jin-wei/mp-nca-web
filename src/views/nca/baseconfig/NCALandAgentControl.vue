<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">基本设置</a></li>
            <li class="active">地产商维护</li>
        </ol>
        <!-- end breadcrumb -->

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'地产商列表'"></DomainChooseHeader>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="搜索地产商名称" v-model="search_text" style="width: 180px">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="landAgentTable"></table>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增地产商</h4>
                    </div>
                    <form @submit.prevent="addAct" id="formA">
                        <div class="modal-body row">
                            <div class="form-group col-xs-6">
                                <label class=""><span class="table-required">*</span>地产商名称:</label>
                                <input class="form-control " v-model="workRow.landagent_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>联系电话:</label>
                                <input class="form-control" v-model="workRow.landagent_phone" maxlength="100" data-parsley-maxlength="20">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>联系人:</label>
                                <input class="form-control" v-model="workRow.landagent_contact" maxlength="100" data-parsley-maxlength="30">
                            </div>
                            <div class="form-group col-xs-6">
                                <label>联系地址:</label>
                                <input class="form-control" v-model="workRow.landagent_address" maxlength="100" data-parsley-maxlength="100">
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCALandAgentControl?method=';
    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    import Constant from '../../../assets/js/constant'

    export default {
        data: function() {
            return {
                userinfo: common.getStoreData('userinfo'),
                apiName: common.getApiName(apiUrl),
                workRow: {},
                oldRow: '',
                pagePara: {},
                search_text: null,
                masterTable: null,
                listTitle: ''
            }
        },
        name: 'materielControl',
        components: {
            DomainChooseHeader
        },
        mounted: async function() {
            this.masterTable = $('#landAgentTable');
            this.initData();
        },
        methods: {
            rowDelete: function (msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, () => {
                    this.$http.post(apiUrl + 'delete', row).then(response => {
                        this.masterTable.bootstrapTable("refresh")
                        common.dealSuccessCommon('删除成功');
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                });
            },
            queryParams: function (params) {
                let domain_id = common.getSelect2Val('domain_select');
                if (domain_id) {
                    params.domain_id = domain_id
                }
                params.search_text = this.search_text;
                return JSON.stringify(params)
            },
            initTable: function () {
                window.tableEvents = {
                    'click .tableEdit': (e, value, row, index) => {
                        Constant.setData('userGroup', this.pagePara.userGroup);
                        this.$router.push({
                            path: '/nca/baseconfig/NCALandAgentTeamControl',
                            query: row
                        })
                    },
                    'click .tableDelete': (e, value, row, index) => {
                        this.rowDelete('删除', apiUrl, row, 'landagent_id');
                    }
                };
                this.masterTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEditableLeft('landagent_name', '地产商名称'),
                        common.BTRowFormatEditable('landagent_phone', '联系电话'),
                        common.BTRowFormatEditableLeft('landagent_contact', '联系人'),
                        common.BTRowFormatEdTextareaWidthAlignLeft('landagent_address', '联系地址',200,10),
                        common.BTRowFormat('domain_name', '所属机构'),
                        common.actFormatter('act', () => {
                            return `<a class="btn btn-xs btn-info tableEdit">地产商团队维护</a>&nbsp;<a class="btn btn-xs btn-info tableDelete">删除</a>`
                        }, tableEvents)
                    ],
                    idField: 'landagent_id',
                    uniqueId: 'landagent_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: () => {
                        common.DynamicEditableByDomain(this, this.masterTable)
                    },
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modify', row, 'landagent_id', this.masterTable)
                    }
                })
                common.changeTableClass(this.masterTable)
            },
            initData: async function () {
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;
                $('#domain_select').on('change', (evt) => {
                    this.masterTable.bootstrapTable("refresh")
                });
                $('#formA').parsley();
                this.initTable();
                common.reSizeCall();
            },
            addM: function(event) {
                this.workRow = {};
                $('#formA').parsley().reset();
                $('#AddModal').modal('show')
            },
            addAct: async function(event) {
                try {
                    if ($('#formA').parsley().isValid()) {
                        let response = await this.$http.post(apiUrl + 'add', this.workRow);
                        common.dealSuccessCommon('增加成功');
                        this.masterTable.bootstrapTable("refresh");
                        $('#AddModal').modal('hide')
                    }
                } catch (error) {
                    common.dealErrorCommon(this, error);
                }
            },
            queryConfirm: function(event) {
                this.masterTable.bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
