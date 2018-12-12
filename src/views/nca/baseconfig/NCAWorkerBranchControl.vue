<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">运营数据管理</a></li>
            <li class="active">工人列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <!--<DomainChooseHeader :pagePara="pagePara" :apiName="apiName"></DomainChooseHeader>-->
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">工人列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" v-model="search_text" placeholder="搜索电话号、姓名" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="workerTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/baseconfig/NCAWorkerControl?method=';
//    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'
    export default {
        data: function() {
            return {
                pagePara: {},
                rowData: {},
                oldRow: {},
                bindData: {},
                search_text: null,
                apiName: common.getApiName(apiUrl),
                domain_type: 0
            }
        },
        name: 'workerControl',
        components: {
//            DomainChooseHeader
        },
        mounted: function() {
            this.masterTable = $('#workerTable');
            this.initPage();
        },
        methods: {
            queryParams: function (params) {
                params.search_text = this.search_text;
                params.state=1;
                return JSON.stringify(params)
            },
            getDomainType: function () {
                let userinfo = common.getStoreData('userinfo');
                this.domain_type = userinfo.domain_type
            },
            initTable: function () {
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
                        common.BTRowFormat('phone', '电话号'),
                        common.BTRowFormatAlignLeft('name', '姓名'),
                        common.BTRowFormatWithFormatterAlignLeft('user_remark', '备注',common.remarkFormatter),
                        common.BTRowFormatWithFW('created_at', '注册日期', value => {
                            let date = new Date(value);
                            return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}`;
                        }),
                        common.BTRowFormatWithFW('state', '状态', value => {
                            if(value==1){
                                return "启用"
                            }else{
                                return "停用"
                            }
                        })
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
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass(this.masterTable)
            },
            initPage: async function () {
                let response = await this.$http.post(apiUrl + 'init', {})
                let retData = response.data.info
                this.pagePara = retData;
                this.initTable()
                common.reSizeCall()
            },
            search: function(event) {
                this.masterTable.bootstrapTable("refresh", this.queryParams)
            }
        }
    }
</script>
<style scoped>
</style>
