<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript: history.back()">采购管理</a></li>
            <li class="active">物料采购订单</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">物料采购订单</h4>
                    </div>

                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" multiple id="search_text" placeholder="搜索物料编码，名称" style="width: 250px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="printPuchase"><i class="fa fa-print">打印</i></button>
                            </div>
                            <div class="form-group  pull-right allMoney">
                                <span class="pull-right-span">总金额:{{sumMoney}}</span>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <table id="purchaseDetailTable"></table>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>
<script>
    import Constant from '../../../assets/js/constant'
    const common = require('commonFunc');
    const apiUrl = '/api/nca/purchasemanage/NCAPurchaseDetailControl?method=';
    export default {
        data: function() {
            return {
                pagePara: {},
                workRow: {},
                oldRow: '',
                purchaseorder_id:'',
                sumMoney:0
            }
        },
        name: 'NCAPurchaseDetailControl',
        mounted: function() {
            let _self = this;
            let $table = $('#purchaseDetailTable');
            function queryParams(params) {
                params.purchaseorder_id = _self.purchaseorder_id;
                params.search_text=$('#search_text').val();
                return JSON.stringify(params)
            }
            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.sumMoney = res.info.sumMoney;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编号'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatAlignRight('purchase_number', '数量'),
                        common.BTRowFormatWithFormatterAlignRight('purchase_price', '单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('purchase_money', '金额',common.priceFormat),
                        common.BTRowFormat('order_ids', '销售订单号')
                    ],
                    sortOrder:'asc',
                    idField: 'purchasedetail_id',
                    uniqueId: 'purchasedetail_id',
                    toolbar: '#toolbar',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($table)
            }


            async function initPage() {
                let response = await _self.$http.post(apiUrl + 'initAct', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                _self.purchaseorder_id =  common.getUrlParams('purchaseorder_id');
                initTable();
                common.reSizeCall();
            }

            $(function() {
                initPage();
            });
        },
        methods: {
            search: function(event) {
                $('#purchaseDetailTable').bootstrapTable("refresh")
            },
            printPuchase: function () {
                let _self = this;
                Constant.setData('materielInfo', this.pagePara);
                _self.$router.push({
                    path: '/nca/purchasemanage/NCAPuchasePirntControl',
                    query: {
                        purchaseorder_id: _self.purchaseorder_id
                    }
                });
            }
        }
    }
</script>
<style scoped>
    .allMoney {
        font-size: 14px;
        line-height: 33px;
    }

</style>
