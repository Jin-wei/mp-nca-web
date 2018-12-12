<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">系统管理</a></li>
        <li class="active">内容分类维护</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">内容分类维护</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <select class="form-control select2" multiple id="contentTypeOneSearch"></select>
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">
                            <i class="glyphicon glyphicon-plus"></i> 新增内容分类列表
                        </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="contentTypeTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade " id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增内容分类列表</h4>
                </div>
                <form @submit.prevent="addType" id="formA">
                    <div class="modal-body row">
                        <div class="form-group">
                            <select class="form-control select2" id="contentTypeOneAdd" data-parsley-required="true"></select>
                        </div>
                        <div class="form-group">
                            <input class="form-control" v-model="work.name" data-parsley-required="false" placeholder="二级分类名称">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">确认</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCACms?method=';

export default {
    data: function() {
        return {
            oldRow:[],
            work:{},
            pagePara:{}
        }
    },
    name: 'contentManageType',

    mounted: function() {
        this.contentTypeTable = $('#contentTypeTable');
        this.initPage();
        this.initTable()
    },
    methods: {
        rowDelete: function (msg, apiUrl, row, key) {
            common.dealConfrimCommon(msg, () => {
                let params = {
                    typeId:row._id,
                    typeInfo:row
                }
                params.typeInfo.active = 0;
                this.$http.post(apiUrl + 'update_t', params).then(response => {
                    this.contentTypeTable.bootstrapTable("refresh")
                    common.dealSuccessCommon('删除成功');
                }, (response) => {
                    common.dealErrorCommon(this, response);
                });
            });
        },

        queryParams: function (params) {
            params.type=common.getSelect2Val('contentTypeOneSearch');
            params.active=1;
            return JSON.stringify(params)
        },
        initTable: function () {
            window.tableEvents = {
                'click .deleteType': (e, value, row, index) => {
                    this.rowDelete('删除', apiUrl, row, 'landagent_id');
                }
            };
            this.contentTypeTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'get_t',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatEdSelect2Disabled(this, 'type', '一级分类', 'content_type_one'),
                    common.BTRowFormatEditable('name', '二级分类'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-primary btn-xs m-r-5 deleteType btn-info-delete">删除</a>';
                    }, tableEvents)
                ],
                idField: '_id',
                uniqueId: '_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableSave: (field, row, oldValue, $el) => {
                    let params = {
                        typeId:row._id,
                        typeInfo:row
                    };
                    params.typeInfo.name=row.name;
                    this.$http.post(apiUrl + 'update_t', params).then(response => {
                        this.contentTypeTable.bootstrapTable("refresh");
                        common.dealSuccessCommon('修改成功');
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                }
            })
            common.changeTableClass(this.contentTypeTable)
        },
        initPage:async function () {
            $('#formMod').parsley();

            let response = await this.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            this.pagePara = response.data.info;
            common.initSelect2Placeholder($('#contentTypeOneAdd'), retData.content_type_one, '一级分类');
            common.initSelect2Placeholder($('#contentTypeOneSearch'), retData.content_type_one, '一级分类');

        },
        search: function(event) {
            this.contentTypeTable.bootstrapTable("refresh", this.queryParams)
        },
        addM: function(event) {
            $('#contentTypeOneAdd').val(null).trigger('change');
            this.work={};
            $('#AddModal').modal('show')
        },
        addType: async function(event) {
            if ($('#formA').parsley().isValid()) {
                this.work.type = common.getSelect2Val('contentTypeOneAdd');
                let response = await this.$http.post(apiUrl + 'add_t', this.work);
                this.contentTypeTable.bootstrapTable('refresh');
                $('#formA').parsley().reset();
                $('#AddModal').modal('hide');
            }
        },
        editSignDate: function () {

        }
    }
}
</script>
<style scoped>
</style>
