<template>
<div>
    <div class="panel panel-inverse" style="height: 540px">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <button id="addM" class="btn btn-info btn-xs" v-show="userDetail.state == 1" @click="addM">新增工作经历记录</button>
                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
            </div>
            <h4 class="panel-title">工作经历详情</h4>
        </div>
        <div>
            <div class="panel-body auto-height">
                <table id="propertyAcceptanceTable"></table>
            </div>
        </div>
        <div class="modal fade" id="propertyAcceptanceModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增工作经历记录</h4>
                    </div>
                    <form @submit.prevent="submitForm" id="formYSX">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">开始时间</label>
                                <div class="col-sm-8">
                                    <input type="text" class="form-control" id="experience_start_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">结束时间</label>
                                <div class="col-sm-8">
                                    <input type="text" class="form-control" id="experience_end_date">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>岗位</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.position_name" id="position_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">证明人</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.witness" id="witness" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label"  style="padding-right: 2px">证明人手机</label>
                                <div class="col-sm-10" style="padding-left:13px">
                                    <input class="form-control" v-model="workRow.witness_phone" id="witness_phone" data-parsley-phone="true">
                                </div>
                            </div>
                            <div class="col-sm-12">
                                <label class="col-sm-2 control-label" style="padding-right: 2px">工作经历描述</label>
                                <div class="col-sm-10 remark-input" style="padding-left:13px">
                                    <textarea class="form-control" v-model="workRow.experience_remark" id="experience_remark" maxlength="200" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">保存</button>
                        </div>
                    </form>
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
            userDetail: {}
        }
    },
    name: 'NCAEWorkExperienceControl',
    watch: {
        pagePara: function() {
            let _self = this;
            if($('#experience_start_date').val()){
                params.experience_start_date = $('#experience_start_date').val()
            }
            if($('#experience_end_date').val()){
                params.experience_end_date = $('#experience_end_date').val()
            }
            common.initDatepicker($('#experience_start_date'));
            common.initDatepicker($('#experience_end_date'));
        },
        employee: function() {
            let _self = this
            let $roomTypeTable = $('#propertyAcceptanceTable');

            function getData() {
                _self.$http.post(apiUrl + 'search_d', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info.userDetail;
                    let userDetail = response.data.info.userDetail[0];
                    _self.userDetail = JSON.parse(JSON.stringify(userDetail))
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            function queryParams(params) {
                common.initImageViewer()
                params.user_id =_self.employee.userDetail[0].user_id
                return JSON.stringify(params);
            }

            function initTable() {
//                $roomTypeTable.bootstrapTable('destroy');
                $roomTypeTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_d',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('position_name', '岗位'),
                        common.BTRowFormatAlignLeft('witness', '证明人'),
                        common.BTRowFormat('witness_phone', '证明人手机'),
                        common.BTRowFormat('experience_start_date', '开始时间'),
                        common.BTRowFormat('experience_end_date', '结束时间'),
                        common.BTRowFormatWithFormatterAlignLeft('experience_remark', '工作经历描述',common.remarkFormatter)
                    ],
                    idField: 'work_experience_id',
                    uniqueId: 'work_experience_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    height: 400,
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($roomTypeTable)
            }
            initTable();
            getData();
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#experience_start_date').val(null).trigger('change')
            $('#experience_end_date').val(null).trigger('change')
            $('#formYSX').parsley().reset()
            $('#propertyAcceptanceModal').modal('show')
        },
        submitForm: async function(event) {
            let _self = this;
            let formYSX = $('#formYSX')
            if (formYSX.parsley().isValid()) {
              let param = {
                user_id: _self.employee.userDetail[0].user_id,
                experience_start_date: $("#experience_start_date").val(),
                experience_end_date: $("#experience_end_date").val(),
                position_name: $("#position_name").val(),
                witness: $("#witness").val(),
                witness_phone: $("#witness_phone").val(),
                experience_remark: $("#experience_remark").val()
            }

            _self.$http.post(apiUrl + 'add_e', param).then((response)=>{
                let retData = response.data.info;
                console.log('retData',retData)
                $('#propertyAcceptanceTable').bootstrapTable("refresh");
                $('#propertyAcceptanceModal').modal('hide')
                param = {};
                common.dealSuccessCommon('已提交');
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
            }
        }
    }
}
</script>
<style scoped>
</style>
