<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" v-show="userDetail.state == 1" @click="clickDone">保存</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">员工信息详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">姓名</span>
                                    <input class="form-control" v-model="userDetail.name" id="username" data-parsley-maxlength="60" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">邮箱</span>
                                    <input class="form-control" v-model="userDetail.email" id="email" data-parsley-type="email">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">手机</span>
                                    <input class="form-control" v-model="userDetail.phone" id="phone" data-parsley-phone="true" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">性别</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="gender"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">部门</span>
                                    <div>
                                        <input class="form-control" v-model="userDetail.department_name" name='p_usergroup_id' id="p_usergroup_id" data-parsley-required="true" disabled>
                                        <!--<select class="form-control select2" style="width:100%" id="p_usergroup_id"></select>-->
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="col-sm-10 no-padding">
                                    <div class="input-group">
                                        <span class="input-group-addon">岗位</span>
                                        <div>
                                            <input class="form-control" v-model="userDetail.position_name" id="usergroup_id" data-parsley-required="true" disabled>
                                            <!--<select class="form-control select2" style="width:100%" id="usergroup_id"></select>-->
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-2" style="padding: 7px 0 0 15px">
                                    <label class="btn btn-info btn-xs fileupload-button" v-on:click="changeU">选择</label>
                                </div>
                            </div>

                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">入职时间</span>
                                    <input type="text" class="form-control" id="entry_date" v-model="userDetail.entry_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">工号</span>
                                    <input class="form-control" v-model="userDetail.user_id" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">汇报对象</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="direct_leadership"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">状态</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="state"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">兼职部门</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="parttime_usergroup_id"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">QQ号</span>
                                    <input class="form-control" v-model="userDetail.qq_no" id="qq_no">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <!--<div class="form-group col-sm-4">-->
                                <!--<div class="input-group">-->
                                    <!--<span class="input-group-addon">级别</span>-->
                                    <!--<div>-->
                                        <!--<select class="form-control select2" style="width:100%" id="project_type5"></select>-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">微信号</span>
                                    <input class="form-control" v-model="userDetail.wechat_no" id="wechat_no">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">报销职级</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="customer_reimburserank_id" data-parsley-required="true"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <userDepartmentGroup :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userDepartmentGroup>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';

import userDepartmentGroup from './userDepartmentGroup.vue'
export default {
    props: ['employee', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            userDetail: {},
            postArray: {},
            pagePara2:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue()
        }
    },
    name: 'NCAEEssentialInformationControl',
    components: {
        userDepartmentGroup
    },
    watch: {
        pagePara: function() {
            let _self = this;
            if($('#entry_date').val()){
                params.entry_date = $('#entry_date').val()
            }
            common.initDatepicker($('#entry_date'));
//            common.initSelect2($('#p_usergroup_id'), _self.pagePara.pGroupList);
            common.initSelect2($('#gender'), _self.pagePara.genderInfo);
            common.initSelect2($('#state'), _self.pagePara.userStation);
            common.initSelect2($('#direct_leadership'), _self.pagePara.staffInfo);
            common.initSelect2($('#parttime_usergroup_id'), _self.pagePara.pGroupList);
//            common.initSelect2($('#usergroup_id'), _self.pagePara.roleList);
            common.initSelect2($('#state2'), _self.pagePara.contractState);
            common.initSelect2($('#customer_reimburserank_id'), _self.pagePara.reimbursList);
        },
        employee: function() {
            let _self = this;

            function getData() {
                _self.$http.post(apiUrl + 'search_d', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info.userDetail;
                    let userDetail = response.data.info.userDetail[0];
                    _self.userDetail = JSON.parse(JSON.stringify(userDetail))
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }

//            function initData() {
//                let mValue = $('#p_usergroup_id').val()
//                if (mValue) {
//                    _self.$http.post(apiUrl + 'usergroup', {
//                        p_usergroup_id: mValue
//                    }).then(response => {
//                        _self.postArray = response.data.info.groupList;
//                        common.initSelect2($('#usergroup_id'), _self.postArray);
//                        $('#usergroup_id').val(_self.postArray.usergroup_id).trigger('change');
//                    }, (response) => {
//                        common.dealErrorCommon(_self, response);
//                    });
//                }
//            }

            $('#gender').val(_self.employee.userDetail[0].gender).trigger('change');
//            $('#p_usergroup_id').val(_self.employee.userDetail[0].p_usergroup_id).trigger('change');
            $('#state').val(_self.employee.userDetail[0].state).trigger('change');
            $('#direct_leadership').val(_self.employee.userDetail[0].direct_leadership).trigger('change');
            $('#parttime_usergroup_id').val(_self.employee.userDetail[0].parttime_usergroup_id).trigger('change');
//            $('#usergroup_id').val(_self.employee.userDetail[0].usergroup_id).trigger('change');
            $('#customer_reimburserank_id').val(_self.employee.userDetail[0].customer_reimburserank_id).trigger('change');
//            initData()
            getData()
        }
    },
    methods: {
        clickDone: function() {
            let _self = this;
            if ($('#phone').val() == '') {
                return common.dealPromptCommon('请填写手机号')
            }  else if ($('#phone').val() != '') {
                let re = /^1[3|5|8]{1}[0-9]{9}$/;//以1开始后面加10位数字
                if (re.test($('#phone').val())) {
                    let eValue = $('#email').val();
                    if(eValue){
                        let re = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+((.[a-zA-Z0-9_-]{2,3}){1,2})$/;//以1开始后面加10位数字
                        if (re.test($('#email').val())) {
                            console.log('$(\'#phone\').val()',$('#email').val());
                        } else {
                            return common.dealPromptCommon('邮箱格式错误');
                        }
                    }
                    let param = {
                        user_id: _self.employee.userDetail[0].user_id,
                        username: $("#username").val(),
                        email: $("#email").val(),
                        phone: $("#phone").val(),
                        gender: common.getSelect2Val('gender'),
                        department_id: _self.pagePara2.userDepartmentId,
                        position_id: _self.pagePara2.userPositionId,
                        // p_usergroup_id: _self.pagePara2.userDepartmentId,
                        entry_date: $("#entry_date").val(),
                        parttime_usergroup_id: common.getSelect2Val('parttime_usergroup_id'),
                        customer_reimburserank_id: common.getSelect2Val('customer_reimburserank_id'),
                        usergroup_id: _self.pagePara2.userGroupId,
                        direct_leadership: common.getSelect2Val('direct_leadership'),
                        state: common.getSelect2Val('state'),
                        qq_no: $("#qq_no").val(),
                        wechat_no: $("#wechat_no").val(),
                        info: '1'
                    }
                    common.dealConfrimCommon('是否修改该员工信息？', async () => {
                        _self.$http.post(apiUrl + 'modify', param).then((response)=>{
                            common.dealSuccessCommon('已提交');
                        }, (error)=>{
                            common.dealErrorCommon(_self, error)
                        });
                    });
                } else {
                    return common.dealPromptCommon('手机号格式错误');
                }
            }
        },
        changeU: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-departmentgroupselect-dialog');
        },
        departmentGroupSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'change_group', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#p_usergroup_id").val(_self.pagePara2.userDepartmentName)
                    $("#usergroup_id").val(_self.pagePara2.userPositionName)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                });
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
