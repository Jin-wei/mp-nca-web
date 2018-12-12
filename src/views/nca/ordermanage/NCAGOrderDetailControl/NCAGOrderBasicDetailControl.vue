<template>
<div>
    <div class="row">
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn" v-show="!pagePara.thirdUser">
                    <button class="btn btn-info btn-xs" v-on:click="saveOrder">保存</button>
                </div>
                <h4 class="panel-title">订单信息</h4>
            </div>
            <div class="panel-body">
                <div class="row">
                    <div class="form-group col-sm-4">
                        <div class="input-group">
                            <span class="input-group-addon">户型</span>
                            <div>
                                <input disabled class="form-control" v-model="order.name">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">建筑面积</span>
                            <div>
                                <input disabled class="form-control" v-model="order.order_house_area">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group">
                            <span class="input-group-addon">楼盘</span>
                            <div>
                                <input disabled class="form-control" v-model="order.estate_name">
                            </div>
                        </div>
                    </div>
                </div>
                <div class="row">
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">状态</span>
                            <div class="">
                                <select class="form-control select2" id="order_state" disabled></select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">监理</span>
                            <div>
                                <select class="form-contro select2" style="width:100%" id="order_supervision" v-bind:disabled="pagePara.thirdUser"></select>
                            </div>
                        </div>
                    </div>

                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">工长</span>
                            <div>
                                <select class="form-contro select2" style="width:100%" id="order_foreman" v-bind:disabled="pagePara.thirdUser"></select>
                            </div>
                        </div>
                    </div>

                </div>
                <div class="row">
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">房屋地址</span>
                            <div>
                                <input class="form-control" disabled v-model="order.order_address">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group">
                            <span class="input-group-addon">计划开工日期</span>
                            <div>
                                <input disabled class="form-control" v-model="order.break_date">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">业主</span>
                            <div>
                                <input disabled class="form-control" v-model="order.username">
                            </div>
                        </div>
                    </div>
                </div>

                <div class="row">
                    <div class="form-group col-sm-12">
                        <div class="input-group ">
                            <span class="input-group-addon">订单描述</span>
                            <div>
                                <textarea placeholder="请输入业主要求：例如背景墙，天花，木门细节需求" rows="5" class="form-control" style="resize:none;" v-model="order.order_remark"></textarea>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-6">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                    </div>
                    <h4 class="panel-title">空间列表</h4>
                </div>
                <div class="panel-body">
                    <table id="orderRoomTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="row">
        <div class="col-md-6">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                    </div>
                    <h4 class="panel-title">服务团队</h4>
                </div>
                <div class="panel-body">
                    <table id="staffTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const orderApiUrl = '/api/nca/ordermanage/NCAGOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            workRow: {},
            oldRow: ''
        }
    },
    name: 'NCAGOrderBasicDetailControl',
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#order_state'), _self.pagePara.orderStateInfo);
            common.initSelect2($('#order_foreman'), _self.pagePara.foremanf);
        },
        order: function() {

            let _self = this;
            let $orderRoomTable = $('#orderRoomTable');
            let $staffTable = $('#staffTable');

            function roomTypeFormatter(value, row) {
                for (let i = 0; i < _self.pagePara.roomTypeInfo.length; i++) {
                    if (_self.pagePara.roomTypeInfo[i].id === value) {
                        return _self.pagePara.roomTypeInfo[i].text
                    }
                }
                return ''
            }

            function initOrderRoomTable() {
                $orderRoomTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFormatter('room_type', '空间类型', roomTypeFormatter),
                        common.BTRowFormat('room_name', '空间名称'),
                    ],
                    idField: 'room_id',
                    uniqueId: 'room_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true
                });

                $orderRoomTable.bootstrapTable('load', {
                    data: _self.order.roomsInfo
                })
                common.changeTableClass($orderRoomTable)
            }

            function roleFormatter(value, row) {
                for (let i = 0; i < _self.pagePara.roleInfo.length; i++) {
                    if (_self.pagePara.roleInfo[i].id === value) {
                        return _self.pagePara.roleInfo[i].text
                    }
                }
                return ''
            }

            function initStaffTable() {
                $staffTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFormatterAlignLeft('staff_type', '角色', roleFormatter),
                        common.BTRowFormatAlignLeft('name', '姓名'),
                        common.BTRowFormat('staff_phone', '手机号')
                    ],
                    idField: 'id',
                    uniqueId: 'id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true
                });
                $staffTable.bootstrapTable('load', {
                    data: _self.order.staffInfo
                })
                common.changeTableClass($staffTable)
            }

            $('#order_state').val(_self.order.order_state).trigger('change')
            $('#order_foreman').val(_self.order.order_foreman).trigger('change');
            $('#order_supervision').html('')
            common.initSelect2($('#order_supervision'), _self.order.supervisionInfo);
            $('#order_supervision').val(_self.order.order_supervision ? _self.order.order_supervision : null).trigger('change');
            initStaffTable()
            initOrderRoomTable()
        }
    },
    methods: {
        saveOrder: async function(event) {
            let _self = this;
            try {
              _self.order.order_supervision = common.getSelect2Val('order_supervision');
              _self.order.order_foreman = common.getSelect2Val('order_foreman');
              let response = await _self.$http.post(orderApiUrl + 'saveOrder', _self.order);
              _self.$parent.refreshOrder();

            } catch (error) {
                common.dealErrorCommon(_self, error);
            }

        }
    }
}
</script>
<style scoped>
</style>
