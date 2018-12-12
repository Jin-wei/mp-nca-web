<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">品质管理</a></li>
            <li class="active">品质数据录入列表</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">品质数据录入列表</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="search_text" placeholder="搜索采购单号，供应商名" id="search_textM" style="width: 230px">
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="created_quality" placeholder="开始时间"/>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="qualityInTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/purchasemanage/NCAQualityAddControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            masterTable: null,
            search_text: null
        }
    },
    name: 'NCAQualityAddControl',
    methods: {
        queryParamsOther: function (params) {
            if($('#created_quality').val()){
                params.created_at = $('#created_quality').val()
            }
            params.search_text = this.search_text;
            return JSON.stringify(params);
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams);
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
                console.log('init success');
            }, (response) => {
                console.log('init error');
                common.dealErrorCommon(this, response)
            });
            this.initTable(0);
        },
        initTable:function(){
            let _self = this;
            window.tableEvents = {
                'click .stock': (e, value, row, index) => {
                    this.$router.push({path: '/nca/purchasemanage/NCAQualityAddDetailControl', query: row});
                }
            };

            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParamsOther,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('receipt_id', '收货单号'),
                    common.BTRowFormat('purchaseorder_id', '采购单号'),
                    common.BTRowFormatAlignLeft('supplier_name', '供应商名称'),
                    common.BTRowFormat('created_at', '创建日期'),
                    common.BTRowFormatEdSelect2Disabled(this, 'check_state', '检验状态', 'checkInfo'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 stock">品质录入</a>';
                    }, tableEvents)
                ],
                idField: 'receipt_id',
                uniqueId: 'receipt_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {

                },
                onEditableSave: function(field, row, oldValue, $el) {

                }
            });
            common.changeTableClass(this.masterTable)
        }
    },
    mounted: function() {
        this.masterTable = $('#qualityInTable');
        this.initPage();
        common.initDatepicker($('#created_quality'));
    }
}
</script>
<style scoped>
</style>
