<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">总部订单查询</a></li>
        <li class="active">订单详情</li>
    </ol>
    <div class="row hide-order-detail-desk" style="display:none;">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <span class="panel-title">订单进度</span>
                    <span style="padding-left: 30px;">订单号：{{order.order_id}}</span>
                    <small style="padding-left: 20px;">{{order.name + '|' + order.phone + '|' + order.order_address}}</small>
                </div>
                <div class="panel-body">
                    <timeLine :order="order"></timeLine>
                </div>
            </div>
            <div class="panel panel-inverse panel-with-tabs">
                <div class="nav nav-tabs p-0">
                    <div class="panel-heading-btn m-r-10 m-t-10">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-success" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <!-- begin nav-tabs -->
                    <div class="tab-overflow overflow-right">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="setData('orderBasicControlOD', $event)"><a href="#orderBasicDetailControl" data-toggle="tab">订单详情</a></li>
                            <li class="" @click="setData('designControlOD', $event)"><a href="#designControl" data-toggle="tab">方案设计</a></li>
                            <li class="" @click="setData('materialControlOD', $event)"><a href="#materialControl" data-toggle="tab">物料管理</a></li>
                            <li class="" @click="setData('acceptancePlanControlOD', $event)"><a href="#acceptancePlanControl" data-toggle="tab">验收计划</a></li>
                        </ul>
                    </div>
                </div>
                <div class="tab-content">
                    <div class="tab-pane fade active in" id="orderBasicDetailControl">
                        <orderBasicDetailControl :order="orderBasicControlOD" :pagePara="pagePara"></orderBasicDetailControl>
                    </div>
                    <div class="tab-pane fade" id="designControl">
                        <designControl :order="designControlOD" :pagePara="pagePara"></designControl>
                    </div>
                    <div class="tab-pane fade" id="materialControl">
                        <materialControl :order="materialControlOD" :pagePara="pagePara"></materialControl>
                    </div>
                    <div class="tab-pane fade" id="acceptancePlanControl">
                        <acceptancePlanControl :order="acceptancePlanControlOD"></acceptancePlanControl>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAHDOrderDetailControl?method=';

import orderBasicDetailControl from './NCAHDOrderBasicDetailControl.vue'
import materialControl from './NCAHDMaterialListControl.vue'
import designControl from './NCAHDDesignControl.vue'
import timeLine from '../NCAOrderDetailControl/timeLine.vue'
import acceptancePlanControl from './NCAHDAcceptancePlanControl.vue'

export default {
    data: function() {
        return {
            order: {},
            tabInfo: '',
            pagePara: {},
            orderBasicControlOD: {},
            designControlOD: {},
            materialControlOD: {},
            acceptancePlanControlOD: {}
        }
    },
    components: {
        orderBasicDetailControl,
        timeLine,
        materialControl,
        designControl,
        acceptancePlanControl
    },
    name: 'NCAHDOrderDetailControl',
    mounted: async function() {
        let _self = this;

        async function getInit() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {order_id:_self.$route.query.orderId});
                _self.pagePara = $.extend(true, {}, response.data.info);

                $('.hide-order-detail-desk').show();
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        async function getOrder() {
            let orderId = _self.$route.query.orderId
            let param = {
                order_id: orderId
            };
            try {
                let response = await _self.$http.post(apiUrl + 'search_order', param);
                _self.order = response.data.info;
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        await getInit();
        await getOrder();
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
        },
        refreshOrder: function(tabData, event) {
            let _self = this;

            function getOrder() {
                let orderId = common.getUrlParams("orderId");
                let param = {
                    order_id: orderId
                }
                _self.$http.post(apiUrl + 'search_order', param).then((response) => {
                    _self.order = response.data.info;
                    _self.setData(_self.tabInfo)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            getOrder()
        }
    }
}
</script>
<style scoped>
</style>
