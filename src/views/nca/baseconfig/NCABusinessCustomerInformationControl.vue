<template>
    <div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button class="btn btn-info btn-xs" v-on:click="saveOrder">保存</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">企业客户详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表姓名</span>
                                    <input class="form-control" v-model="business.legalperson_contact" id="legalperson_contact" data-parsley-required="true" data-parsley-maxlength="60">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">法人联系方式</span>
                                    <input class="form-control" v-model="business.legalperson_phone" id="legalperson_phone" data-parsley-required="true" data-parsley-maxlength="20">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表微信</span>
                                    <input class="form-control" v-model="business.legalperson_contact_wechat" id="legalperson_contact_wechat" data-parsley-required="true" data-parsley-maxlength="60">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">法人代表QQ</span>
                                    <input class="form-control" v-model="business.legalperson_contact_qq" id="legalperson_contact_qq" data-parsley-required="true" data-parsley-maxlength="20">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人姓名</span>
                                    <input class="form-control" v-model="workRow.domain_contact" data-parsley-required="true" data-parsley-maxlength="60" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人手机</span>
                                    <input class="form-control" v-model="workRow.domain_phone" data-parsley-required="true" data-parsley-maxlength="20" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人微信</span>
                                    <input class="form-control" v-model="business.business_contact_wechat" id="business_contact_wechat" data-parsley-required="true" data-parsley-maxlength="60">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">联系人QQ</span>
                                    <input class="form-control" v-model="business.business_contact_qq" id="business_contact_qq" data-parsley-required="true" data-parsley-maxlength="20">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">结算方式</span>
                                    <select class="form-control select2" style="width:100%"  id="settlementway" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">月结天数</span>
                                    <input class="form-control" v-model="business.mouthsettlement" id="mouthsettlement" data-parsley-required="true" data-parsley-maxlength="20">
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
    const apiUrl = '/api/nca/baseconfig/NCABusinessCustomerControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                business: {},
                workRow: {},
                oldRow: ''
            }
        },
        name: 'NCABusinessCustomerInformationControl',
        mounted: function() {
            let _self = this
            _self.workRow = _self.$route.query;
            _self.domain_phone = _self.workRow.domain_phone
            _self.domain_contact = _self.workRow.domain_contact
            $('#domain_phone').html(_self.domain_phone)
            $('#domain_contact').html(_self.domain_contact)


            async function initData() {
                await _self.getPagePara()
                common.initSelect2($('#settlementway'), _self.pagePara.settletInfo)
                common.reSizeCall();
                await _self.getPagePara2()
            }
            initData()


        },
        methods: {
            getPagePara: async function(event) {
                let _self = this
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    let retData = response.data.info;
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            getPagePara2: async function(event) {
                let _self = this
                try {
                    _self.business={};
                    this.$http.post(apiUrl + 'StaffControlSRV_search', {
                        domain: _self.$route.query.domain
                    }).then((response) => {
                        let retData = response.data.info;
                        _self.business = retData.rows[0]
                        $('#settlementway').val(_self.business.settlementway).trigger('change')
                    }, (response) => {
                        common.dealErrorCommon(this, response)
                    });
                    console.log('success')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }

            },
            saveOrder: function() {
                let _self = this
                let param = {
                    domain:_self.$route.query.domain,
                    business_contact_qq: $("#business_contact_qq").val(),
                    business_contact_wechat: $("#business_contact_wechat").val(),
                    legalperson_contact: $("#legalperson_contact").val(),
                    legalperson_phone: $("#legalperson_phone").val(),
                    legalperson_contact_qq: $("#legalperson_contact_qq").val(),
                    legalperson_contact_wechat: $("#legalperson_contact_wechat").val(),
                    settlementway: common.getSelect2Val('settlementway'),
                    mouthsettlement: $("#mouthsettlement").val()?$("#mouthsettlement").val():0,
                }
                _self.$http.post(apiUrl + 'save', param).then((response)=>{
                    common.dealSuccessCommon('已提交');
                }, (error)=>{
                    common.dealErrorCommon(_self, error)
                });
            }
        }
    }
</script>
<style scoped>
</style>
