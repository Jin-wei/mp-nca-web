<template>
<div>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button class="btn btn-info btn-xs" v-on:click="saveOrder">保存</button>
                </div>
                <h4 class="panel-title">订单信息</h4>
            </div>
            <div class="panel-body">
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
                            <span class="input-group-addon">销售</span>
                            <div>
                                <select class="form-contro select2" style="width:100%" id="order_sales_id"></select>
                            </div>
                        </div>
                    </div>

                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">采购方</span>
                            <div>
                                <select class="form-contro select2" style="width:100%" id="purchase_domain_id" disabled></select>
                            </div>
                        </div>
                    </div>

                </div>
                <div class="row">
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">采购单号</span>
                            <div>
                                <input class="form-control" disabled v-model="order.purchase_order_id">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group">
                            <span class="input-group-addon">采购联系人</span>
                            <div>
                                <input disabled class="form-control" v-model="order.purchase_contact">
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-4">
                        <div class="input-group ">
                            <span class="input-group-addon">采购联系电话</span>
                            <div>
                                <input disabled class="form-control" v-model="order.purchase_phone">
                            </div>
                        </div>
                    </div>
                </div>

                <div class="row">
                    <div class="form-group col-sm-12">
                        <div class="input-group ">
                            <span class="input-group-addon">订单描述</span>
                            <div>
                                <textarea placeholder="请输入需要备注的信息" rows="5" class="form-control" style="resize:none;" v-model="order.order_remark"></textarea>
                            </div>
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
const orderApiUrl = '/api/nca/ordermanage/NCASOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            workRow: {},
            oldRow: ''
        }
    },
    name: 'NCASOrderBasicDetailControl',
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#order_state'), _self.pagePara.orderStateInfo);
            common.initSelect2($('#order_sales_id'), _self.pagePara.employeeInfo);
            common.initSelect2($('#purchase_domain_id'), _self.pagePara.domainInfo);
        },
        order: async function() {
            let _self = this;

            $('#order_state').val(_self.order.order_state).trigger('change')
            $('#order_sales_id').val(_self.order.order_sales_id).trigger('change')
            $('#purchase_domain_id').val(_self.order.purchase_domain_id).trigger('change')

          async function getBasicInfo() {
            try {
              let param = {
                domain_id: _self.order.purchase_domain_id
              }
              let response = await _self.$http.post(orderApiUrl + 'search_basicInfo', param);
              let retData = response.data.info;
              _self.$set(_self.order, 'purchase_contact', retData.purchase_contact)
              _self.$set(_self.order, 'purchase_phone', retData.purchase_phone)
            } catch (error) {
              common.dealErrorCommon(_self, error)
            }
          }

          await getBasicInfo();
        }
    },
    methods: {
        saveOrder: async function(event) {
            let _self = this;
            try {
              _self.order.order_sales_id = common.getSelect2Val('order_sales_id');
              let response = await _self.$http.post(orderApiUrl + 'saveOrder', _self.order);
              _self.$parent.refreshOrder();
              common.dealSuccessCommon('订单已保存');
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
