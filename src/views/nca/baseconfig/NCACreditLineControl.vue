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
                            <button class="btn btn-info btn-xs" @click="searchDetail" id="searchDetail">查看明细</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">信用额度管理</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">信用额度</span>
                                    <input class="form-control" v-model="corporate.creditline_money" id='creditline_money' onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">信用额度结余金额</span>
                                    <input class="form-control" v-bind:value="corporate.creditline_money - corporate.creditline_use" id='creditline_use' readonly>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">预付款结余金额</span>
                                    <input class="form-control" v-bind:value="corporate.creditline_advance" id='creditline_advance' readonly>
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
    methods: {
        addDEC: function(event) {
            let _self = this;

            let param = {
                corporateclients_id: _self.corporate.corporateclients_id,
                creditline_money:_self.corporate.creditline_money
            }

            _self.$http.post(apiUrl + 'modifyCreditLine', param).then((response) => {
                common.dealSuccessCommon('保存成功');
            }, (error) => {
                common.dealErrorCommon(_self, error)
            });

        },
        searchDetail:function(){
            let _self = this;
            _self.$router.push({
                path: '/nca/baseconfig/NCACreditLineDetailtControl',
                query: {
                    corporateclients_id: _self.corporate.corporateclients_id
                }
            })
        }
    }
}
</script>
<style scoped>
</style>
