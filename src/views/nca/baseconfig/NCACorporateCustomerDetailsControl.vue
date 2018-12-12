<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <!--<form @submit.prevent="addAct" id="corporateForm">-->
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button class="btn btn-info btn-xs" @click="addDEC" id="addDEC">保存</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">企业客户详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表姓名</span>
                                    <input class="form-control" v-model="corporate.legalperson_contact" id='legalperson_contact' maxlength="60" data-parsley-maxlength="60">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">法人联系方式</span>
                                    <input class="form-control" v-model="corporate.legalperson_phone" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" id='legalperson_phone' data-parsley-phone="true" maxlength="11" data-parsley-maxlength="11">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表微信</span>
                                    <input class="form-control" v-model="corporate.legalperson_wechat" id='legalperson_wechat' maxlength="20" data-parsley-maxlength="20">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表QQ</span>
                                    <input class="form-control" v-model="corporate.legalperson_qq" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" id='legalperson_qq' maxlength="11" data-parsley-maxlength="11">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人姓名</span>
                                    <input class="form-control" v-model="corporate.corporateclients_contact" id='corporateclients_contact' maxlength="60" data-parsley-maxlength="60">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人手机</span>
                                    <input class="form-control" v-model="corporate.corporateclients_phone" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" id='corporateclients_phone' data-parsley-phone="true" maxlength="11" data-parsley-maxlength="11">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人微信</span>
                                    <input class="form-control" v-model="corporate.corporateclients_contact_wechat" id='corporateclients_contact_wechat' maxlength="20" data-parsley-maxlength="20">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人QQ</span>
                                    <input class="form-control" v-model="corporate.corporateclients_contact_qq" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" id='corporateclients_contact_qq' maxlength="11" data-parsley-maxlength="11">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">结算方式</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="settlement_way"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">月结天数</span>
                                    <input type="text" class="form-control" v-model="corporate.month_settlement" id="month_settlement" maxlength="60" data-parsley-maxlength="60">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <!--</form>-->
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCACorporateClientsControl?method=';

export default {
    props: ['corporate', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {}
        }
    },
    name: 'NCACorporateCustomerDetailsControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        corporate: function() {
            let _self = this;
            common.initSelect2($('#settlement_way'), _self.pagePara.settletInfo);
            $('#settlement_way').val(_self.corporate.settlement_way).trigger('change');
        }
    },
    methods: {
        addAct: function(event) {},
        addDEC: function(event) {
            let _self = this;
            // if ($('#corporateForm').parsley().isValid()) {
                let param = {
                    corporateclients_id: _self.corporate.corporateclients_id,
                    legalperson_contact: $("#legalperson_contact").val(),
                    legalperson_phone: $("#legalperson_phone").val(),
                    legalperson_wechat: $("#legalperson_wechat").val(),
                    legalperson_qq: $("#legalperson_qq").val(),
                    corporateclients_contact: $("#corporateclients_contact").val(),
                    corporateclients_phone: $("#corporateclients_phone").val(),
                    corporateclients_contact_wechat: $("#corporateclients_contact_wechat").val(),
                    corporateclients_contact_qq: $("#corporateclients_contact_qq").val(),
                    settlement_way: common.getSelect2Val('settlement_way'),
                    month_settlement: $('#month_settlement').val()
                }

                _self.$http.post(apiUrl + 'modify', param).then((response) => {
                    common.dealSuccessCommon('已提交');
                }, (error) => {
                    common.dealErrorCommon(_self, error)
                });
            // }
        }
    }
}
</script>
<style scoped>
</style>
