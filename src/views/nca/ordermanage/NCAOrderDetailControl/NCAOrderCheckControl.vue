<template>
    <div>
        <div>
            <div class="panel-heading" v-if="!checkState('WORKING')">
                <div class="row">
                    <div class="col-md-12">
                        <div class="panel-heading-btn" >
                            <button v-if="checkState('REVIEWING')" class="btn btn-info" v-on:click="startOrderClick()">
                                开工
                            </button>
                        </div>
                    </div>
                </div>
            </div>
            <div class="panel panel-inverse">
                <div class="panel-body">
                    <table id="order-check-table"></table>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method='


    export default {
        props: ['order', 'pagePara'],
        data: function() {
            return {
                userinfo: common.getStoreData('userinfo'),
                orderReviews:null
            }
        },
        name: 'orderCheckControl',
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

                    function dataFormatter(value, row, index) {
                        if (value) {
                            return common.dateTimeFormat(value)
                        }else {
                            return null
                        }
                    }
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
                          common.BTRowFormatWithFormatter('review_date', '评审日期', dataFormatter)
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

                function getReviews () {
                  _self.$http.post(apiUrl + 'search_order_review', {orderId: _self.order.order_id}).then((response) => {
                    let retdata = response.data.info;
                    _self.orderReviews = response.data.info;
                    $('#order-check-table').bootstrapTable('load', {
                      data: retdata
                    })
                  }, (response) => {
                    common.dealErrorCommon(_self, response)
                  })
                }

                function initPage() {
                    initTable();
                    getReviews();
                    common.initImageViewer();
                }

                $(function() {
                    initPage();
                })
            }
        },
        methods: {
            startOrderClick:function () {
                let _self = this;
                _self.$http.post(apiUrl + 'start_order', {orderId: _self.order.order_id}).then((response) => {
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
