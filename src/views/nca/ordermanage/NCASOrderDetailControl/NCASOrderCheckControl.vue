<template>
    <div>
        <div class="panel panel-inverse" style="margin-top: 10px">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button v-if="orderReviews != null && orderReviews.length == 0" class="btn btn-info btn-xs" v-on:click="startOrderReviewClick()">开始审核</button>
                    <button v-if="checkState('PAYED') && !checkState('SHIPPED')" class="btn btn-info btn-xs" v-on:click="shippedGoodsClick()">发货</button>
                </div>
                <h4 class="panel-title">订单评审</h4>
            </div>
            <div class="panel-body auto-height">
                <table id="order-check-table"></table>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/ordermanage/NCASOrderDetailControl?method='


    export default {
        props: ['order', 'pagePara'],
        data: function() {
            return {
                userinfo: common.getStoreData('userinfo'),
                orderReviews:null
            }
        },
        name: 'NCASOrderCheckControl',
        mounted: function() {

        },
        watch: {
            pagePara: function() {
                let _self = this
            },
            order: function() {
                let _self = this
                let $table = $('#order-check-table')

                function initTable() {
                    function statusFormatter(value, row, index) {
                        for (let state of _self.pagePara.orderReviewStatusInfo) {
                            if (state.id == value) {
                                return state.text;
                            }
                        }
                    }
                    function userFormatter(value, row, index) {
                        for (let employee of _self.pagePara.employeeInfo) {
                            if (value == employee.id) {
                                return employee.text;
                            }
                        }
                        return null;
                    }

                    $table.bootstrapTable({
                        columns: [
                          common.BTRowFormatWithIndex('NO.'),
                          common.BTRowFormatAlignLeft('require_name', '评审项目'),
                          common.BTRowFormatWithFormatterAlignLeft('require_description', '评审责任描述',common.remarkFormatter),
                          common.BTRowFormatWithFormatterAlignLeft('duty_user_id', '责任人', userFormatter),
                          common.BTRowFormatWithFormatterAlignLeft('review_description', '评审提交内容',common.remarkFormatter),
                          common.BTRowFormatWithFormatter('review_status', '状态', statusFormatter),
                          common.BTRowFormat('review_date', '评审日期')
                        ],
                        idField: 'review_id',
                        uniqueId: 'review_id',
                        clickToSelect: true,
                        locale: 'zh-CN',
                        onEditableShown: function(field, row, $el, editable) {
//                        _self.oldRow = $.extend(true, {}, row)
                        },
                        onEditableSave: function(field, row, oldValue, $el) {
                            let param = {
                                reviewId: row.review_id,
                                dutyUserId:row.duty_user_id,
                                order_id: _self.order.order_id
                            }
                            _self.$http.post(apiUrl + 'set_order_review_duty', param).then((response) => {
                                _self.$parent.refreshOrder();
                            }, (response) => {
                                common.dealErrorCommon(_self, response)
                            })
                        },
                        onPostBody: function() {
                            $('[data-toggle="popover"]').each(function () {
                                $(this).popover()
                            })
                        }
                    });
                    common.changeTableClass($table);
                }

                function initPage() {
                    initTable();
                }

                $(function() {
                    initPage();
                })

                _self.$http.post(apiUrl + 'search_order_review', {orderId: _self.order.order_id}).then((response) => {
                    let retdata = response.data.info;
                    _self.orderReviews = response.data.info;
                    $table.bootstrapTable('load', {
                        data: retdata
                    })
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                common.initImageViewer()
            }
        },
        methods: {
            startOrderReviewClick:function () {
                let _self = this;
                _self.$http.post(apiUrl + 'start_order_review', {orderId: _self.order.order_id,domainId:_self.userinfo.domain_id}).then((response) => {
                    _self.$parent.refreshOrder();
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
          shippedGoodsClick:function () {
                let _self = this;
                _self.$http.post(apiUrl + 'shipped_goods', {orderId: _self.order.order_id}).then((response) => {
                    _self.$parent.refreshOrder();
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            checkState: function(state, event) {
                let _self = this
                if (_self.order.statedict) {
                    if (state in _self.order.statedict) return true
                }
                return false
            }
        }
    }
</script>
<style scoped>
</style>
