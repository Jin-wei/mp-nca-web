<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" v-show="userDetail.state != 0" @click="clickDone3">保存</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">员工信息详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">合同名称</span>
                                    <input class="form-control" v-model="userContract.contract_name" id="contract_name">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">签约人</span>
                                    <input class="form-control" v-model="userContract.sign_name" id="sign_name">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">所属部门</span>
                                    <div>
                                        <input class="form-control" v-model="userDetail.department_name" name='p_usergroup_id2' id="p_usergroup_id2" data-parsley-required="true" disabled>
                                        <!--<select class="form-control select2" style="width:100%" id="p_usergroup_id2"></select>-->
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">所属岗位</span>
                                    <div>
                                        <input class="form-control" v-model="userDetail.position_name" id="usergroup_id2" data-parsley-required="true" disabled>
                                        <!--<select class="form-control select2" style="width:100%" id="usergroup_id2"></select>-->
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">薪资模式</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="user_form"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">合同编号</span>
                                    <input class="form-control" v-model="userContract.contract_no" id="contract_no">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">开始日期</span>
                                    <input type="text" class="form-control" id="start_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">结束日期</span>
                                    <input type="text" class="form-control" id="end_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">合同状态</span>
                                    <div>
                                        <select class="form-control select2" style="width:100%" id="contract_state"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">试用期结束日期</span>
                                    <input type="text" class="form-control" id="probation_end_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">转正日期</span>
                                    <input type="text" class="form-control" id="official_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">基础工资</span>
                                    <input class="form-control" v-model="userContract.base_salary" id="base_salary" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">能力工资</span>
                                    <input class="form-control" v-model="userContract.capacity_salary" id="capacity_salary" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">绩效工资</span>
                                    <input class="form-control" v-model="userContract.performance_salary" id="performance_salary" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">工资总额</span>
                                    <input class="form-control" v-model="userContract.total_salary" id="total_salary" onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');">
                                </div>
                            </div>
                        </div>
                        <div class="row" style="margin-bottom: 9px;">
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">开户行</span>
                                    <input class="form-control" v-model="userContract.deposit_bank" id="deposit_bank">
                                </div>
                            </div>
                            <div class="form-group col-sm-4">
                                <div class="input-group">
                                    <span class="input-group-addon">银行账户</span>
                                    <input class="form-control" v-model="userContract.bank_account" id="bank_account">
                                </div>
                            </div>
                        </div>
                        <div class="form-group">
                            <div style="margin-left: 30px">
                                <label class="btn btn-info btn-xs fileupload-button">上传附件
                                    <input id='designUpload' type="file" name="file">
                                </label>
                            </div>
                        </div>
                        <div class="form-group">
                            <div style="margin-left: 30px">
                                <div class="image-content" v-for="(name, index) in upload2">
                                    <h7>{{name}}</h7>
                                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-danger delete-btn" @click="removImage(index)">
                                        <i class="fa fa-times"></i>
                                    </a>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">附件信息</h4>
                    </div>
                    <div class="panel-body">
                        <table id="noticeFilesTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';
import fileupload from '../../../components/common/fileUpload.vue'

function generateUploadFile(_self, fileInfo) {
    if (_self) {
        let convert = {}
        convert.file_name = fileInfo.name;
        convert.file_url = fileInfo.url;
        convert.file_type = fileInfo.type;
        _self.files2.push(convert)
        console.log('success1')
    }
}

export default {
    props: ['employee', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            userDetail: {},
            postArray: {},
            userContract: {},
            upload2: [],
            files2: []
        }
    },
    components: {fileupload},
    name: 'NCAEPersonnelContractControl',
    watch: {
        pagePara: function() {
            let _self = this;
            if($('#start_date').val()){
                params.start_date = $('#start_date').val()
            }
            if($('#end_date').val()){
                params.end_date = $('#end_date').val()
            }
            if($('#probation_end_date').val()){
                params.probation_end_date = $('#probation_end_date').val()
            }
            if($('#official_date').val()){
                params.official_date = $('#official_date').val()
            }
            common.initDatepicker($('#start_date'));
            common.initDatepicker($('#end_date'));
            common.initDatepicker($('#probation_end_date'));
            common.initDatepicker($('#official_date'));
            // common.initSelect2($('#p_usergroup_id2'), _self.pagePara.pGroupList);
            // common.initSelect2($('#usergroup_id2'), _self.pagePara.roleList);
            common.initSelect2($('#user_form'), _self.pagePara.contractInfo);
            common.initSelect2($('#contract_state'), _self.pagePara.contractState);
        },
        employee: function() {
            let _self = this;
            let $fileTable = $('#noticeFilesTable');

            function getData() {
                _self.$http.post(apiUrl + 'search_d', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info.userDetail;
                    let userDetail = response.data.info.userDetail[0];
                    let userContract = response.data.info.userContract[0];
                    _self.userDetail = JSON.parse(JSON.stringify(userDetail))
                    _self.userContract = JSON.parse(JSON.stringify(userContract))
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
                    _self.upload2.push(fileInfo.uploadurl.name)
                    let convert = {}
                    convert.file_name = fileInfo.uploadurl.name;
                    convert.file_url = fileInfo.uploadurl.url;
                    convert.file_type = fileInfo.uploadurl.type;
                    _self.files2.push(convert)
                    console.log('success')
                });
            }

            function initFileTable() {
                let columns;
                columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
                ]

                $fileTable.bootstrapTable('destroy');

                $fileTable.bootstrapTable({
                    columns: columns,
                    height: 100,
                    idField: 'user_contract_id',
                    uniqueId: 'user_contract_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($fileTable)
            }

            // $('#p_usergroup_id2').val(_self.employee.userDetail[0].p_usergroup_id).trigger('change');
            // $('#usergroup_id2').val(_self.employee.userDetail[0].usergroup_id).trigger('change');
            $('#user_form').val(_self.employee.userDetail[0].user_form).trigger('change');
            $('#contract_state').val(_self.employee.userContract[0].contract_state).trigger('change');
            $('#start_date').val(_self.employee.userContract[0].start_date).trigger('change');
            $('#end_date').val(_self.employee.userContract[0].end_date).trigger('change');
            $('#probation_end_date').val(_self.employee.userContract[0].probation_end_date).trigger('change');
            $('#official_date').val(_self.employee.userContract[0].official_date).trigger('change');


            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_files', {
                        user_contract_id: _self.employee.userContract[0].user_contract_id
                    });
                    let retdata = response.data.info;
                    $('#noticeFilesTable').bootstrapTable('load', {
                        data: retdata.designs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            function initPage() {
            getData()
            initFileTable()
            getTablesData(_self)
            }
            initPage()
        }
    },
    methods: {
        clickDone3: function() {
            let _self = this

            let param ={
                user_id: _self.employee.userDetail[0].user_id,
                user_contract_id: _self.employee.userContract[0].user_contract_id,
                username: $("#username").val(),
                phone: $("#phone").val(),
                contract_name: $("#contract_name").val(),
                sign_name: $("#sign_name").val(),
                user_form: common.getSelect2Val('user_form'),
                department_id: _self.employee.userDetail[0].department_id,
                position_id: _self.employee.userDetail[0].position_id,
                usergroup_id: _self.employee.userDetail[0].usergroup_id,
                // p_usergroup_id: common.getSelect2Val('p_usergroup_id2'),
                contract_state: common.getSelect2Val('contract_state'),
                start_date: $("#start_date").val(),
                end_date: $("#end_date").val(),
                probation_end_date: $("#probation_end_date").val(),
                official_date: $("#official_date").val(),
                base_salary: $("#base_salary").val(),
                capacity_salary: $("#capacity_salary").val(),
                performance_salary: $("#performance_salary").val(),
                total_salary: $("#total_salary").val(),
                deposit_bank: $("#deposit_bank").val(),
                bank_account: $("#bank_account").val(),
                contract_no: $("#contract_no").val(),
                files: _self.files2
            }

            _self.$http.post(apiUrl + 'modify_c', param).then((response)=>{
                let retData = response.data.info;
//                    window.history.back()
                _self.files2 = []
                common.dealSuccessCommon('已提交');
                async function getTablesData() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_files', {
                            user_contract_id: _self.employee.userContract[0].user_contract_id
                        });
                        let retdata = response.data.info;
                        $('#noticeFilesTable').bootstrapTable('load', {
                            data: retdata.designs
                        });
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
                getTablesData(_self)
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        removImage: async function(index) {
            let _self = this
            _self.upload2.splice(index, 1)
            _self.files2.splice(index, 1)
        },
        //upload component callback
        startUpload(e) {
            // file upload start event
        },
        finishUpload(fileInfo) {
            // file upload finish event
            let _self = this
            generateUploadFile(_self, fileInfo)
        },
        failUpload(message) {
            common.dealWarningCommon(message)
        },
        progress(e) {
            // file upload progress
            // returns false if progress is not computable
        }
    }
}
</script>
<style scoped>
</style>
