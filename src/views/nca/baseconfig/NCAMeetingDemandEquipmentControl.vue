<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">设备信息</h4>
                    </div>

                    <div class="panel-body">
                        <table id="meetingDemandEquipmentTable"></table>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMeetingMinuteControl?method=';

export default {
    props: ['meeting', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            pagePara2:{},
            meeting_remark_state: ''
        }
    },
    name: 'NCAMeetingDemandEquipmentControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            let $meetingDemandEquipmentTable = $('#meetingDemandEquipmentTable');
            _self.meeting_remark_state = _self.meeting.meeting_remark_state

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init_e', {meetingroom_id: _self.meeting.meetingroom_id});
                let retData = response.data.info;
                _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#meetingroomequipment_id'), _self.pagePara2.equipmentList);
            }

            function queryParams(params) {
//                common.initImageViewer()
                params.meeting_id =_self.meeting.meeting_id
                return JSON.stringify(params);
            }

            function initTable() {
                $meetingDemandEquipmentTable.bootstrapTable({
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
                        common.BTRowFormatAlignRight('equipment_num', '数量')
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
                common.changeTableClass($meetingDemandEquipmentTable)
            }
            $(function() {
                initData();
                initTable();
            })
        }
    },
    methods: {

    }
}
</script>
<style scoped>
</style>
