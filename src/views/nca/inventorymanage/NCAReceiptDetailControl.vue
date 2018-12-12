<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">收货单列表</a></li>
            <li class="active">收货单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">收货单&emsp;&emsp;&emsp;收货单号：{{queryData.receipt_id}}</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" v-model="search_text" placeholder="搜索物料名称、编码" style="width: 230px">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <table id="receiptDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/inventorymanage/NCAReceiptListControl?method=';

export default {
    data: function() {
        return {
            pagePara: {},
            masterTable: null,
            queryData: {},
            search_text: null
        }
    },
    name: 'NCAReceiptDetailControl',
    methods: {
        queryParams: function (params) {
            params.receipt_id = this.queryData.receipt_id;
            params.search_text = this.search_text;
            return JSON.stringify(params);
        },
        search: function() {
            this.masterTable.bootstrapTable("refresh");
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', {}).then(response => {
                this.pagePara = response.data.info;
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
            this.initTable();
        },
        initTable:function(){
            let _self = this;
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_receipt_item',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns:  [
                  common.BTRowFormatWithIndex('NO.'),
                  common.BTRowFormat('materiel_code', '物料编码'),
                  common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                  common.BTRowFormat('materiel_format', '规格型号'),
                  common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                  common.BTRowFormatAlignRight('receipt_item_number', '收货数量')
                ],
                idField: 'receiptitem_id',
                uniqueId: 'receiptitem_id',
                striped: true,
                clickToSelect: false,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            });
            common.changeTableClass(this.masterTable)
        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        this.masterTable = $('#receiptDetailTable');
        this.initPage();
    }
}
</script>
<style scoped>
</style>
