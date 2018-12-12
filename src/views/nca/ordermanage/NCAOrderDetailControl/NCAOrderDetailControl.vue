<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="/nca/ordermanage/NCAOrderControl">订单管理</a></li>
        <li class="active">订单详情</li>
    </ol>
    <div class="row hide-order-detail-desk" style="display:none;">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <span class="panel-title">订单详情</span>
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
                            <li class="active" @click="setData('orderBasicControlOD', $event)"><a href="#orderBasicDetailControl" data-toggle="tab">订单详情</a>
                            </li>
                            <li class="" @click="setData('designControlOD', $event)"><a href="#designControl" data-toggle="tab">方案设计</a>
                            <!--</li>-->
                            <li class="" @click="setData('materialControlOD', $event)"><a href="#materialControl" data-toggle="tab">物料管理</a></li>
                            <li class="" @click="setData('internalCheckControlOD', $event)"><a href="#internalCheckControl" data-toggle="tab">内部审核</a></li>
                            <li class="" @click="setData('contractControlOD', $event)"><a href="#contractControl" data-toggle="tab">合同管理</a></li>
                            <li v-if="pagePara.orderReviewInfo != null && pagePara.orderReviewInfo.length > 0" class="" @click="setData('orderCheckControlOD', $event)"><a href="#orderCheckControl" data-toggle="tab">订单评审</a></li>
                            <li class="" @click="setData('planControlOD', $event)"><a href="#planControl" data-toggle="tab">施工计划</a></li>
                            <li class="" @click="setData('acceptancePlanControlOD', $event)"><a href="#acceptancePlanControl" data-toggle="tab">验收计划</a></li>
                            <li class="" @click="setData('changeControlOD', $event)"><a href="#changeControl" data-toggle="tab">变更管理</a></li>
                            <li class="" @click="setData('moneyControlOD', $event)"><a href="#moneyControl" data-toggle="tab">收款记录</a></li>

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
                    <div class="tab-pane fade" id="internalCheckControl">
                        <internal-check-control :order="internalCheckControlOD" :pagePara="pagePara"></internal-check-control>
                    </div>
                    <div class="tab-pane fade" id="contractControl">
                        <contract-control :order="contractControlOD" :pPagePara="pagePara"></contract-control>
                    </div>
                    <div class="tab-pane fade" id="orderCheckControl">
                        <order-check-control :order="orderCheckControlOD" :pagePara="pagePara"></order-check-control>
                    </div>
                    <div class="tab-pane fade" id="planControl">
                        <plan-control :order="planControlOD" :pagePara="pagePara"></plan-control>
                    </div>
                    <div class="tab-pane fade" id="acceptancePlanControl">
                        <acceptancePlanControl :order="acceptancePlanControlOD"></acceptancePlanControl>
                    </div>
                    <div class="tab-pane fade" id="changeControl">
                        <change-control :order="changeControlOD" :pagePara="pagePara"></change-control>
                    </div>
                    <div class="tab-pane fade" id="moneyControl">
                        <money-control :order="moneyControlOD" :pagePara="pagePara"></money-control>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';

import orderBasicDetailControl from './NCAOrderBasicDetailControl.vue'
import materialControl from './NCAMaterialListControl.vue'
import designControl from './NCADesignControl.vue'
import timeLine from './timeLine.vue'
import planControl from './NCAPlanControl.vue'
import acceptancePlanControl from './NCAAcceptancePlanControl.vue'
import changeControl from './NCAChangeControl.vue'
import contractControl from './NCAContractControl.vue'
import internalCheckControl from './NCAInternalCheckControl.vue'
import orderCheckControl from './NCAOrderCheckControl.vue'
import moneyControl from './NCAMoneyControl.vue'

export default {
    data: function() {
        return {
            order: {},
            tabInfo: '',
            pagePara: {},
            orderBasicControlOD: {},
            designControlOD: {},
            materialControlOD: {},
            planControlOD: {},
            acceptancePlanControlOD: {},
            changeControlOD: {},
            contractControlOD: {},
            internalCheckControlOD: {},
            orderCheckControlOD: {},
            moneyControlOD: {}
        }
    },
    components: {
        orderBasicDetailControl,
        planControl,
        contractControl,
        timeLine,
        materialControl,
        designControl,
        changeControl,
        acceptancePlanControl,
        internalCheckControl,
        orderCheckControl,
        moneyControl
    },
    name: 'NCAOrderDetailControl',
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
        console.log('init success')
    },
    watch: {
        order: function() {
            let _self = this;
            if (_self.order && !_self.tabInfo) {
                _self.setData('orderBasicControlOD');
            }
            let state_time = []
            state_time.push(_self.order.statedict.NEW || '-');
            state_time.push(_self.order.statedict.DESIGNING || ' -');
            state_time.push(_self.order.statedict.CHECKING || ' -');
            state_time.push(_self.order.statedict.SIGNED || ' -');
            state_time.push(_self.order.statedict.REVIEWING || ' -');
            state_time.push(_self.order.statedict.SIGNED || ' -');
            state_time.push(_self.order.statedict.WORKING || ' -');
            state_time.push(_self.order.statedict.FINISHI || ' -');
            // window.setTimeout(function () {
            console.log('state_time',state_time)
                if ( state_time[1] != ' -' && state_time[2] == ' -' && state_time[3] == ' -' && state_time[4] == ' -' && state_time[5] == ' -' && state_time[6] == ' -' && state_time[7] == ' -') {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:1
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] == ' -' && state_time[4] == ' -' && state_time[5] == ' -' && state_time[6] == ' -' && state_time[7] == ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:2
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] == ' -' && state_time[5] != ' -' && state_time[6] == ' -' && state_time[7] == ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:3
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] != ' -' && state_time[5] != ' -' && state_time[6] == ' -' && state_time[7] == ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:4
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] != ' -' && state_time[5] != ' -' && state_time[6] == ' -' && state_time[7] == ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:5
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] != ' -' && state_time[5] != ' -' && state_time[6] != ' -' && state_time[7] == ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:6
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] != ' -' && state_time[5] != ' -' && state_time[6] != ' -' && state_time[7] != ' -' ) {
                    let data = {
                        order_id: _self.$route.query.orderId,
                        type:7
                    };
                    let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
                }
            // },500);
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
.tab-background {
    background: #937867
}
</style>
