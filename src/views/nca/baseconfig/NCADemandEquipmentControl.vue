<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button class="btn btn-info btn-xs" v-show="meeting_state !=1 " @click="addDEC" id="addDEC">新增设备</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">设备信息</h4>
                    </div>

                    <div class="panel-body">
                        <table id="demandEquipmentTable"></table>
                    </div>

                    <div class="modal fade" id="demandEquipmentModal">
                        <div class="modal-dialog">
                            <div class="modal-content">
                                <div class="modal-header">
                                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                                    <h4 class="modal-title">新增设备</h4>
                                </div>
                                <form @submit.prevent="submitForm" id="formDEC">
                                    <div class="modal-body row">
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">设备名称</label>
                                            <div class="col-sm-8">
                                                <select class="form-control select2" style="width:100%" id="meetingroomequipment_id" data-parsley-required="true" ></select>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">单位</label>
                                            <div class="col-sm-8">
                                                <input type="text" id="equipment_unit" class="form-control" disabled>
                                            </div>
                                        </div>
                                        <div class="form-group col-sm-6">
                                            <label class="col-sm-4 control-label">数量</label>
                                            <div class="col-sm-8">
                                                <input class="form-control" v-model="workRow.equipment_num" data-parsley-type="number">
                                            </div>
                                        </div>
                                    </div>
                                    <div class="modal-footer">
                                        <button type="submit" class="btn btn-info"><i class="fa fa-fw fa-plus"></i>增加</button>
                                    </div>
                                </form>
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
const apiUrl = '/api/nca/baseconfig/NCAMeetingManageControl?method=';

export default {
    props: ['meeting', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara2: {},
            pagePara3: {},
            meeting_state: ''
        }
    },
    name: 'NCADemandEquipmentControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            let $demandEquipmentTable = $('#demandEquipmentTable');
            _self.meeting_state = _self.meeting.meeting_state

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init_e', {meetingroom_id: _self.meeting.meetingroom_id});
                let retData = response.data.info;
                _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#meetingroomequipment_id'), _self.pagePara2.equipmentList);
            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 accept_appointment btn-info-table">删除</a>'
                ].join('')

            }
            window.tableEvents = {
                'click .accept_appointment': function (e, value, row, index) {
                    common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_e', $demandEquipmentTable, row, 'meetingequipment_id', function() {})
                }
            };
            function queryParams(params) {
//                common.initImageViewer()
                params.meeting_id =_self.meeting.meeting_id
                return JSON.stringify(params);
            }

            function initTable() {
                $demandEquipmentTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_e',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('equipment_name', '设备名称'),
                        common.BTRowFormat('equipment_unit', '单位'),
                        common.BTRowFormatAlignRight('equipment_num', '数量'),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ],
                    idField: 'meetingequipment_id',
                    uniqueId: 'meetingequipment_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($demandEquipmentTable)
            }
            $(function() {
                initData();
                initTable();
                $('#meetingroomequipment_id').change(function() {
                    let mValue = $('#meetingroomequipment_id').val()
                    if(mValue){
                        _self.$http.post(apiUrl + 'search_u', {
                            meetingroomequipment_id: mValue
                        }).then((response) => {
                            let retData = response.data.info;
                            _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                            $("#equipment_unit").val(_self.pagePara3.groupList[0].text);
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    } else if (mValue==null){
                        _self.$http.post(apiUrl + 'search_u', {
                            meetingroomequipment_id: mValue
                        }).then((response) => {
                            let retData = response.data.info;
                            _self.pagePara3 = JSON.parse(JSON.stringify(retData));
                            $("#equipment_unit").val(_self.pagePara3.groupList);
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }
                });
            })
        }
    },
    methods: {
        addDEC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#meetingroomequipment_id').val(null).trigger('change')
            $('#formDEC').parsley().reset()
            $('#demandEquipmentModal').modal('show')
        },
        submitForm: async function(event) {
            let _self = this;
            let formDEC = $('#formDEC')
            if (formDEC.parsley().isValid()) {
                _self.workRow.meeting_id = _self.meeting.meeting_id;
                _self.workRow.meetingroomequipment_id = common.getSelect2Val('meetingroomequipment_id');
                _self.workRow.equipment_unit = $("#equipment_unit").val()

                _self.$http.post(apiUrl + 'add_e', _self.workRow).then((response)=>{
                    let retData = response.data.info;
                    $('#demandEquipmentTable').bootstrapTable("refresh");
                    $('#demandEquipmentModal').modal('hide');
                    _self.workRow = {};
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
