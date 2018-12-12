<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back();">信用额度管理</a></li>
            <li class="active">信用额度详情</li>
        </ol>
        <!-- end breadcrumb -->

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">信用额度详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="搜索单号" v-model="search_text" style="width: 180px">
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="creditlinedetail_type"></select>
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="beginTimeAll" placeholder="起始时间" />
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="endTimeAll" placeholder="结束时间" />
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="creditLineDetailTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

    export default {
        data: function() {
            return {
                search_text:''
            }
        },
        name: 'NCACreditLineDetailtControl',
        mounted: async function() {
            this.corporateclients_id = this.$route.query.corporateclients_id;
            this.creditLineDetailTable = $('#creditLineDetailTable');
            this.initData();
        },
        methods: {

            queryParams: function (params) {
                params.search_text = this.search_text;
                params.corporateclients_id = this.corporateclients_id;
                params.creditlinedetail_type = common.getSelect2Val('creditlinedetail_type');
                params.createdBeginTime = $('#beginTimeAll').val();
                params.createdEndTime = $('#endTimeAll').val();
                return JSON.stringify(params)
            },
            nameFormatter:function(value, row){
                return [
                    '<a class="show_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            },
            initTable: function () {
                window.tableEvents = {
                    'click .show_detail': (e, value, row, index) => {
                        console.log(row.creditlinedetail_businessid)
                        if(row.creditlinedetail_type == 1){
                            this.$router.push({
                                path: '/nca/cashiermanage/NCAGatheringControl',
                                query: {
                                    cashiergathering_code:row.creditlinedetail_businessid
                                }
                            })
                        }else if (row.creditlinedetail_type == 2){
                            this.$router.push({
                                path: '/nca/ordermanage/NCAOrderControl',
                                query: {
                                    order_id:row.order_id
                                }
                            })
                        }
                    }
                };
                this.creditLineDetailTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchCreditLineDetail',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('created_at', '发生时间'),
                        common.BTRowFormatWithFE('creditlinedetail_businessid', '业务单号', this.nameFormatter,tableEvents),
                        common.BTRowFormatWithFormatterAlignRight('creditlinedetail_money', '金额',common.priceFormat),
                        common.BTRowFormatEdSelect2Disabled(this, 'creditlinedetail_type', '业务分类', 'creditLineDetailType'),
                        common.BTRowFormatWithFormatterAlignRight('creditlinedetail_surplus_creditline', '结余信用额度',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('creditlinedetail_surplus_advance', '结余预付款金额',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('total_surplus_money', '可用余额结余',common.priceFormat)
                    ],
                    idField: 'creditlinedetail_id',
                    uniqueId: 'creditlinedetail_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass(this.creditLineDetailTable)
            },
            initData: async function () {
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;

                common.initDatepicker($('#beginTimeAll'));
                common.initDatepicker($('#endTimeAll'));
                common.initSelect2Placeholder($('#creditlinedetail_type'), this.pagePara.creditLineDetailType,'类型');
                this.initTable();
                common.reSizeCall();
            },

            queryConfirm: function(event) {
                this.creditLineDetailTable.bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
