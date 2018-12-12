<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()">订单管理</a></li>
        <li class="active">销售订单详情</li>
    </ol>
    <div class="row hide-order-detail-desk" style="display:none;">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <span>订单详情&emsp;&emsp;&emsp;订单号：{{order.order_id}}</span>
                </div>
                <div class="panel-body">
                    <goTimeLine :order="order"></goTimeLine>
                </div>
            </div>
            <div class="row">
                <div class="col-md-12">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="setData('goOrderBasicControlOD', $event)">
                            <a href="#goOrderBasicControlOD" data-toggle="tab">
                                <span class="visible-xs">订单详情</span>
                                <span class="hidden-xs">订单详情</span>
                            </a>
                        </li>
                        <li class="" @click="setData('goMaterialControlOD', $event)">
                            <a href="#goMaterialControl" data-toggle="tab">
                                <span class="visible-xs">物料清单</span>
                                <span class="hidden-xs">物料清单</span>
                            </a>
                        </li>
                        <li class="" @click="setData('receiveableControlOD', $event)">
                            <a href="#receiveableControl" data-toggle="tab">
                                <span class="visible-xs">收款记录</span>
                                <span class="hidden-xs">收款记录</span>
                            </a>
                        </li>
                        <li class="" @click="setData('goAcceptanceListControlOD', $event)">
                            <a href="#goAcceptanceListControl" data-toggle="tab">
                                <span class="visible-xs">订单评审</span>
                                <span class="hidden-xs">订单评审</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="goOrderBasicControlOD">
                            <goOrderBasicDetailControl :order="goOrderBasicControlOD" :pagePara="pagePara"></goOrderBasicDetailControl>
                        </div>
                        <div class="tab-pane fade in" id="goMaterialControl">
                            <goMaterialControl :order="goMaterialControlOD" :pagePara="pagePara"></goMaterialControl>
                        </div>
                        <div class="tab-pane fade in" id="receiveableControl">
                            <receiveableControl :order="receiveableControlOD" :pagePara="pagePara"></receiveableControl>
                        </div>
                        <div class="tab-pane fade in" id="goAcceptanceListControl">
                            <goAcceptanceListControl :order="goAcceptanceListControlOD" :pagePara="pagePara"></goAcceptanceListControl>
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
const apiUrl = '/api/nca/ordermanage/NCASOrderDetailControl?method=';

import goTimeLine from './NCASTimeLine.vue'
import goOrderBasicDetailControl from './NCASOrderBasicDetailControl.vue'
import goMaterialControl from './NCASMaterialControl.vue'
import receiveableControl from './NCASReceiveableControl.vue'
import goAcceptanceListControl from './NCASOrderCheckControl.vue'

export default {
    data: function() {
        return {
            order: {},
            tabInfo: '',
            pagePara: {},
            goOrderBasicControlOD: {},
            goMaterialControlOD: {},
            receiveableControlOD: {},
            userId: '',
            goAcceptanceListControlOD: {}
        }
    },
    components: {
        goTimeLine,
        goOrderBasicDetailControl,
        goMaterialControl,
        receiveableControl,
        goAcceptanceListControl
    },
    name: 'NCASOrderDetailControl',
    mounted: async function() {
        let _self = this;

        async function getInit() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
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


        async function initTable() {
            let $table = $('#table');
            $table.bootstrapTable({
                height: 100,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('username', '业主姓名'),
                    common.BTRowFormat('phone', '手机号')
                ],
                idField: 'user_id',
                uniqueId: 'user_id',
                locale: 'zh-CN'
            });
            common.changeTableClass($table);
        }

        await getInit();
        await getOrder();
        await initTable();
    },
    watch: {
        order: function() {
            let _self = this;
            if (_self.order && !_self.tabInfo) {
                _self.setData('goOrderBasicControlOD');
            }
            let state_time = []
            state_time.push(_self.order.statedict.NEW || '-');
            state_time.push(_self.order.statedict.PAYED || ' -');
            state_time.push(_self.order.statedict.REVIEWING || ' -');
            state_time.push(_self.order.statedict.SHIPPED || ' -');
            state_time.push(_self.order.statedict.FINISHI || ' -');
            // window.setTimeout(function () {
            console.log('state_time',state_time)
            if ( state_time[1] != ' -' && state_time[2] == ' -' && state_time[3] == ' -' && state_time[4] == ' -') {
                let data = {
                    order_id: _self.$route.query.orderId,
                    type:1
                };
                let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
            } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] == ' -' && state_time[4] == ' -') {
                let data = {
                    order_id: _self.$route.query.orderId,
                    type:2
                };
                let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
            } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] == ' -') {
                let data = {
                    order_id: _self.$route.query.orderId,
                    type:3
                };
                let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
            } else if ( state_time[1] != ' -' && state_time[2] != ' -' && state_time[3] != ' -' && state_time[4] != ' -') {
                let data = {
                    order_id: _self.$route.query.orderId,
                    type:4
                };
                let orderUser = _self.$http.post(apiUrl + 'add_order_user', data);
            }
            // },500);
        }
    },
    methods: {
        addC: function(event) {
            let _self = this;
            $('#table').bootstrapTable("removeAll");
            $('#searchText').val('');
            $('#AddModal').modal('show')
        },
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
        },
        queryConfirm: async function(event) {
            let _self = this;

            if ($('#searchText').val() == '') {
                common.dealPromptCommon('请输入手机号码或客户姓名');
                return
            }
            let param = {
                search_text: $('#searchText').val()
            };
            try {
                $('#table').bootstrapTable("removeAll");
                _self.userId = '';
                let response = await _self.$http.post(apiUrl + 'getCustomer', param);
                let retData = response.data.info;
                _self.userId = retData[0].user_id;
                $('#table').bootstrapTable('insertRow', {
                    index: 0,
                    row: retData[0]
                })
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        },
        relationOrder: async function(event) {
            let _self = this;

            if (_self.userId == '') {
                common.dealPromptCommon('请先查询客户');
                return
            }
            let orderId = common.getUrlParams("orderId");
            let param = {
                order_id: orderId,
                user_id: _self.userId
            };
            try {
                let response = await _self.$http.post(apiUrl + 'relationOrder', param);
                response = await _self.$http.post(apiUrl + 'search_order', param);
                _self.order = response.data.info;
                _self.setData(_self.tabInfo);
                common.dealSuccessCommon('保存成功');
                $('#AddModal').modal('hide');
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }
    }
}
</script>
<style scoped>
#addC {
    position: absolute;
    top: 2px;
    right: 50px;
}
</style>
