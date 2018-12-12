<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">体验店管理</a></li>
            <li class="active">体验馆详情</li>
        </ol>
        <div class="row hide-order-detail-desk" style="display:none;">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <span class="panel-title">客户编号&nbsp;:&nbsp;&nbsp;&nbsp;{{workRow.domain}}</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                        <span class="panel-title">客户名称&nbsp;:&nbsp;&nbsp;&nbsp;{{workRow.domain_name}}</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                        <span class="panel-title">客户地址&nbsp;:&nbsp;&nbsp;&nbsp;{{workRow.domain_address}}</span>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                        <span class="panel-title">客户联系方式&nbsp;:&nbsp;&nbsp;&nbsp;{{workRow.domain_phone}}</span>
                    </div>
                </div>
                <div class="panel panel-inverse panel-with-tabs">
                    <div class="nav nav-tabs p-0">
                        <div class="panel-heading-btn m-r-10 m-t-10">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-success" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <div class="tab-overflow overflow-right">
                            <ul class="nav nav-tabs">
                                <li class="active" @click="setData('businessInformation', $event)"><a href="#businessCustomerInformationControl" data-toggle="tab">企业客户详情</a></li>
                                <li class="" @click="setData('businesssaleprice', $event)"><a href="#businessSalePriceControl" data-toggle="tab">销售报价管理</a></li>
                                <li class="" @click="setData('businesssale', $event)"><a href="#businessSaleOrderControl" data-toggle="tab">销售订单管理</a></li>
                            </ul>
                        </div>
                    </div>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="businessCustomerInformationControl">
                            <businessCustomerInformationControl :order="businessInformation" :pagePara="pagePara"></businessCustomerInformationControl>
                        </div>
                        <div class="tab-pane fade" id="businessSalePriceControl">
                            <businessSalePriceControl :order="businesssaleprice" :pagePara="pagePara"></businessSalePriceControl>
                        </div>
                        <div class="tab-pane fade" id="businessSaleOrderControl">
                            <businessSaleOrderControl :order="businesssale" :pagePara="pagePara"></businessSaleOrderControl>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCABusinessCustomerControl?method=';

    import businessCustomerInformationControl from './NCABusinessCustomerInformationControl.vue'
    import businessSaleOrderControl from './NCABusinessSaleOrderControl.vue'
    import businessSalePriceControl from './NCABusinessSalePriceControl.vue'

    export default {
        data: function() {
            return {
                order: {},
                workRow:{},
                tabInfo: '',
                pagePara: {},
                businessInformation: {},
                businesssale: {},
                businesssaleprice: {},
            }
        },
        components: {
            businessCustomerInformationControl,
            businessSaleOrderControl,
            businessSalePriceControl
        },
        name: 'NCABusinessCustomerDetailControl',
        mounted: async function() {
            let _self = this;

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {domain_id:_self.$route.query.domain_id});
                    _self.pagePara = $.extend(true, {}, response.data.info);
                    _self.workRow = _self.$route.query;
                    $('.hide-order-detail-desk').show();
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            await getInit();
        },
        watch: {
            order: function() {
                let _self = this;
                if (_self.order && !_self.tabInfo) {
                    _self.setData('orderBasicControlOD');
                }
            }
        },
        methods: {
            setData: function(tabData, event) {
                let _self = this;
                _self.tabInfo = tabData;
                _self[tabData] = JSON.parse(JSON.stringify(_self.order));
            }
        }
    }
</script>
<style scoped>
    .tab-background {
        background: #937867
    }
</style>
