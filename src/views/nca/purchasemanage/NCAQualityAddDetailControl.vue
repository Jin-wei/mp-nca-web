<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">品质数据录入管理</a></li>
        <li class="active">品质录入单</li>
    </ol>

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">品质录入单&emsp;&emsp;&emsp;收货单号：{{queryData.receipt_id}}</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" multiple v-model="searchText" placeholder="搜索物料编码、物料名称" style="width: 250px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="searchQualityD"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group pull-right">
                            <button id="addQualityA" class="btn btn-info" v-show="queryData.check_state !=2 " v-on:click="addQD">提交</button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="QualityDetailTable"></table>
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
            masterTable: null,
            pagePara: {},
            queryData: {},
            searchText: null,
            checkDetail: []
        }
    },
    name: 'NCAQualityAddDetailControl',
    methods: {
        queryParams: function (params) {
            params.receipt_id = this.queryData.receipt_id;
            params.search_text = this.searchText;
            return JSON.stringify(params);
        },
        searchQualityD: async function() {
            $('#QualityDetailTable').bootstrapTable("refresh");
        },
        initTable: function () {
            let _self = this;
            window.tableEvents = {
                'click .purchaseSelect': function(e, value, row, index) {
                    if ($(this).prop('checked')) {
                        _self.checkDetail.push(row);
                    } else {
                        _self.checkDetail.splice($.inArray(row, _self.checkDetail), 1);
                    }
                }
            };
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_d',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                columns: [
                    common.actFormatter('checkAct', () => {return `<input type="checkbox" class="purchaseSelect">`}, tableEvents),
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格'),
                    common.BTRowFormatEdSelect2Disabled(this, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatAlignRight('receipt_item_number', '收货数量'),
                    common.BTRowFormatAlignRight('check_num', '待质检数量'),
                    common.BTRowFormatEditableRight('qualified_number', '合格数量', value => {
                        return value || 0;
                    }),
                    common.BTRowFormatEditableRight('unqualified_number', '不合格数量', value => {
                        return value || 0;
                    }),
                    common.BTRowFormatEdTextareaWidthAlignLeft2('remark', '备注', value => {
                        return value || '无';
                    },120,7),
                ],
                sortOrder:'asc',
                idField: 'receiptitem_id',
                uniqueId: 'receiptitem_id',
                toolbar: '#toolbar',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageNumber: 1,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {

                },
                onEditableSave: (field, row, oldValue, $el) => {

                }
            });
            common.changeTableClass(this.masterTable)
        },
        initPage: function () {
            this.$http.post(apiUrl + 'init', { }).then(response => {
                this.pagePara = response.data.info;
                this.initTable();
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
        },
        addQD: function (event) {
            let params={
                purchaseorder_id: this.queryData.purchaseorder_id,
                other_bill_code: String(new Date().getTime()),
                checkDetail:this.checkDetail,
                supplier_id: this.queryData.supplier_id,
                receipt_id: this.queryData.receipt_id
            }

            this.$http.post(apiUrl + 'submit', params).then((response) => {
                let retData = response.data.info;
                $('#QualityDetailTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
                this.checkDetail = []
            }, (response) => {
                common.dealErrorCommon(this, response)
            });
//        }
        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        this.masterTable = $('#QualityDetailTable');
        this.initPage();
    }
}
</script>
<style scoped>
</style>
