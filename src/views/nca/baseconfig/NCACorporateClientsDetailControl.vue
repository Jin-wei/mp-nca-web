<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">企业客户列表</a></li>
            <li class="active">企业客户详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 col-sm-12">
                <div class="widget widget-stats bg-black-lighter">
                    <div class="stats-info">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">客户编号</span>
                                    <div>
                                        <input class="form-control" v-model="corporate.corporateclients_no" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">客户名称</span>
                                    <div>
                                        <input class="form-control" v-model="corporate.corporateclients_name" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">客户地址</span>
                                    <div>
                                        <input class="form-control" v-model="corporate.corporateclients_address" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">客户联系方式</span>
                                    <div style="width:100px;">
                                        <input class="form-control" v-model="corporate.corporateclients_mobile_phone" readonly>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="row">
                    <div class="col-md-12">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="setData('corporateCustomerDetails', $event)">
                                <a href="#CorporateCustomerDetailsControl" data-toggle="tab">
                                    <span class="visible-xs">企业客户详情</span>
                                    <span class="hidden-xs">企业客户详情</span>
                                </a>
                            </li>
                            <li class="" @click="setData('salesPriceInformation', $event)">
                                <a href="#SalesPriceInformationControl" data-toggle="tab">
                                    <span class="visible-xs">销售报价管理</span>
                                    <span class="hidden-xs">销售报价管理</span>
                                </a>
                            </li>
                            <li class="" @click="setData('salesOrderInformation', $event)">
                                <a href="#SalesOrderInformationControl" data-toggle="tab">
                                    <span class="visible-xs">销售订单管理</span>
                                    <span class="hidden-xs">销售订单管理</span>
                                </a>
                            </li>
                            <li class="" @click="setData('creditLine', $event)">
                                <a href="#CreditLineControl" data-toggle="tab">
                                    <span class="visible-xs">信用额度管理</span>
                                    <span class="hidden-xs">信用额度管理</span>
                                </a>
                            </li>
                        </ul>

                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="CorporateCustomerDetailsControl">
                                <corporateCustomerDetailsControl :corporate="corporateCustomerDetails" :pagePara="pagePara"></corporateCustomerDetailsControl>
                            </div>
                            <div class="tab-pane fade" id="SalesPriceInformationControl">
                                <salesPriceInformationControl :corporate="salesPriceInformation" :pagePara="pagePara"></salesPriceInformationControl>
                            </div>
                            <div class="tab-pane fade" id="SalesOrderInformationControl">
                                <salesOrderInformationControl :corporate="salesOrderInformation" :pagePara="pagePara"></salesOrderInformationControl>
                            </div>
                            <div class="tab-pane fade" id="CreditLineControl">
                                <creditLineControl :corporate="creditLine" :pagePara="pagePara"></creditLineControl>
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
const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

import corporateCustomerDetailsControl from './NCACorporateCustomerDetailsControl.vue'
import salesPriceInformationControl from './NCASalesPriceInformationControl.vue'
import salesOrderInformationControl from './NCASalesOrderInformationControl.vue'
import creditLineControl from './NCACreditLineControl.vue'

export default {
    data: function() {
        return {
            pagePara:{},
            corporate: {},
            corporateCustomerDetails: {},
            salesPriceInformation: {},
            salesOrderInformation: {},
            creditLine:{},
            tabInfo: ''
        }
    },
    components: {
        corporateCustomerDetailsControl,
        salesPriceInformationControl,
        salesOrderInformationControl,
        creditLineControl
    },
    name: 'NCACorporateClientsDetailControl',
    mounted: async function() {
        this.getCorporateClientsInformation();
        this.getCorporateClientsDetailInformation();

    },
    watch: {
        corporate: function() {
            if (this.corporate && !this.tabInfo) {
                this.setData('corporateCustomerDetails');
            }
        }
    },
    methods: {
        getCorporateClientsDetailInformation: async function () {
            let corporateclients_id = this.$route.query.corporateclients_id;
            let param = {
                corporateclients_id: corporateclients_id
            };

            try {
                let response = await this.$http.post(apiUrl + 'search_d', param);
                this.corporate = response.data.info.rows[0];
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        getCorporateClientsInformation: async function () {
            try {
                let response = await this.$http.post(apiUrl + 'init');
                this.pagePara = JSON.parse(JSON.stringify(response.data.info));

            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        setData: async function(tabData, event) {
            await this.getCorporateClientsDetailInformation();
            this.tabInfo = tabData;
            this[tabData] = this.corporate;
        },
        refreshEmployee: function(tabData, event) {
            this.getCorporateClientsDetailInformation()
        }
    }
}
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
