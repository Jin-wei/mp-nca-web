<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" v-show='emailState==1' @click="clickDone">保存</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">酷家乐账号信息详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">酷家乐账号</span>
                                    <input class="form-control" v-if="emailState == 1" v-model="userDetail.kujiale_appuid" id="kujiale_appuid" data-parsley-maxlength="60">
                                    <input class="form-control" v-else-if="emailState == 2" v-model="userDetail.kujiale_appuid" data-parsley-maxlength="60" disabled>
                                </div>
                            </div>
                        </div>
                        <span style="color: red">注意：一个乐宜嘉管理端的账号只能绑定一个酷家乐的账号，一个酷家乐的账号也只能绑定一个乐宜嘉管理端的账号，并且绑定后并不能解除！</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';

export default {
    props: ['employee', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            userDetail: {},
            emailState: ''
        }
    },
    name: 'NCAKujialeContractControl',
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#customer_reimburserank_id'), _self.pagePara.reimbursList);
        },
        employee: function() {
            let _self = this;

            function getData() {
                _self.$http.post(apiUrl + 'search_k', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info;
                    _self.userDetail = JSON.parse(JSON.stringify(retdata))
                    if(_self.userDetail.kujiale_appuid == undefined){
                        _self.emailState = 1
                    } else {
                        _self.emailState = 2
                    }
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }
            getData()
        }
    },
    methods: {
        clickDone: function() {
            let _self = this
            let param = {
                user_id: _self.employee.userDetail[0].user_id,
                kujiale_appuid:$("#kujiale_appuid").val()
            }

            _self.$http.post(apiUrl + 'add_k', param).then((response)=>{
                console.log('response',response)
                if(response.body.msg != 'ok'){
                    _self.emailState = 1
                } else {
                    _self.emailState = 2
                }
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
