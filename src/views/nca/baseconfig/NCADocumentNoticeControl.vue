<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">行政办公管理</a></li>
        <li class="active">文件通知列表</li>
    </ol>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">文件通知列表</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form" v-if="showIndex==0">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="searchText" placeholder="搜索文件编号、文件题目" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="in_typevalue" style="width: 150px;">
                                    <option value="">请选择状态</option>
                                    <option v-for="state in pagePara.stateType" v-bind:value="state.id">{{state.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" @click="showTable(0)"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="form-inline" role="form" v-if="showIndex==1">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" v-model="searchTextOut" placeholder="搜索申请单编号、申请人姓名" id="search_textM" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="out_typevalue" style="width: 150px;">
                                    <option value="">请选择状态</option>
                                    <option v-for="state in pagePara.stateType" v-bind:value="state.id">{{state.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="showTable(1)"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="panel-body ">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="showTable(0)">
                            <a href="#documentNotice_in" data-toggle="tab">
                                <span>内部文件</span>
                            </a>
                        </li>
                        <li class="" @click="showTable(1)">
                            <a href="#documentNotice_out" data-toggle="tab">
                                <span>外部文件</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="documentNotice_in">
                            <table id="DNCInTable"></table>
                        </div>
                        <div class="tab-pane fade" id="documentNotice_out" >
                            <table id="DNCOutTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCADocumentNoticeControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            masterTable: [],
            searchText: null,
            searchTextOut: null,
            showIndex:0
        }
    },
    name: 'NCADocumentNoticeControl',
    methods: {
        queryParams: function (params) {
            params.search_text = this.searchText;
            params.read_state = $('#in_typevalue').val();
            params.document_type = 1;
            return JSON.stringify(params);
        },
        queryParamsOther: function (params) {
            params.search_text = this.searchTextOut;
            params.read_state = $('#out_typevalue').val();
            params.document_type = 2;
            return JSON.stringify(params);
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams);
        },
        initPage: async function () {
            await this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
//                common.initSelect2Placeholder($('#read_state'), this.pagePara.stateType, '状态');
                console.log('init success');
            }, (response) => {
                console.log('init error');
                common.dealErrorCommon(this, response)
            });
            this.initTable();
            this.initTableOtherMateriel();
        },
        showTable: async function(index) {
            let _self = this;
            _self.showIndex = index;
            if(index==0){
                $('#DNCInTable').bootstrapTable("refresh");
                // await _self.initTableOtherMateriel()
            }else if (index==1){
                $('#DNCOutTable').bootstrapTable('refresh');
                await _self.initTableOtherMateriel()
            }
        },
        initTable: function (event) {
            let _self = this;
            let $DNCInTable = $('#DNCInTable');
            window.tableEvents = {
                'click .in_delete': (e, value, row, index) => {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_doc', $DNCInTable, row, 'document_id', function() {})
                },
                'click .in_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCADocumentNoticeDetailControl',
                        query: row
                    })
                }
            };
            $DNCInTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_doc',
                queryParams: this.queryParams,
                sidePagination: 'server',
                height: common.getTableHeight(),
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('document_id', '文件编号'),
                    common.BTRowFormatWithFormatterAlignLeft('document_title', '文件题目',common.remarkFormatter),
                    common.BTRowFormat('created_at', '发布时间'),
                    common.BTRowFormat('document_date', '文件生效日期'),
                    common.BTRowFormatEdSelect2Disabled(this, 'read_state', '状态', 'stateType'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 in_detail">查看</a>'+
                            '<a class="btn btn-info btn-xs m-r-5 in_delete">删除</a>';
                    }, tableEvents)

                ],
                sortOrder:'asc',
                idField: 'document_id',
                uniqueId: 'document_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($DNCInTable);
        },
        initTableOtherMateriel:function(event){
            let _self = this;
            let $DNCOutTable = $('#DNCOutTable');
            window.tableEvents = {
                'click .out_delete': (e, value, row, index) => {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_doc', $DNCOutTable, row, 'document_id', function() {})
                },
                'click .out_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCATestOutNoticeControl',
                        query: row
                    })
                }
            };

            $DNCOutTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_doc',
                queryParams: this.queryParamsOther,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('document_id', '文件编号'),
                    common.BTRowFormatWithFormatterAlignLeft('document_title', '文件题目',common.remarkFormatter),
                    common.BTRowFormat('created_at', '发文时间'),
                    common.BTRowFormatAlignLeft('document_unit', '发布单位'),
                    common.BTRowFormatEdSelect2Disabled(this, 'read_state', '状态', 'stateType'),
                    common.BTRowFormatWithFE('', '操作', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 out_detail">查看</a>'+
                            '<a class="btn btn-info btn-xs m-r-5 out_delete">删除</a>';
                    }, tableEvents)
                    /*common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 out_detail">查看</a>'+
                            '<a class="btn btn-info btn-xs m-r-5 out_delete">删除</a>';
                    }, tableEvents)*/
                ],
                idField: 'document_id',
                uniqueId: 'document_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {

                },
                onEditableSave: function(field, row, oldValue, $el) {

                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($DNCOutTable)
        }
    },
    mounted: function() {
        this.masterTable[0] = $('#DNCInTable');
        this.masterTable[1] = $('#DNCOutTable');
        this.initPage();
        this.initTable();
    }
}
</script>
<style scoped>
.select-width {
    width: 100px;
}
</style>
