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
                        <h4 class="panel-title">参会人员详情</h4>
                    </div>

                    <div class="panel-body">
                        <table id="meetingParticipantsTable"></table>
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
            pagePara2:{},
            meeting_remark_state: ''
        }
    },
    name: 'NCAMeetingParticipantsInformationControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            let $meetingParticipantsTable = $('#meetingParticipantsTable');
            _self.meeting_remark_state = _self.meeting.meeting_remark_state

            function queryParams(params) {
//                common.initImageViewer()
                params.meeting_id =_self.meeting.meeting_id
                return JSON.stringify(params);
            }

            function initTable() {
                $meetingParticipantsTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_a',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('attendee_id', '工号'),
                        common.BTRowFormatAlignLeft('name', '姓名'),
                        common.BTRowFormatAlignLeft('p_usergroup_name', '部门'),
                        common.BTRowFormatAlignLeft('usergroup_name', '岗位'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'meetingattendee_state', '状态', 'meetingroomInfo')
                    ],
                    idField: 'meetingattendee_id',
                    uniqueId: 'meetingattendee_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($meetingParticipantsTable)
            }
            $(function() {
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
