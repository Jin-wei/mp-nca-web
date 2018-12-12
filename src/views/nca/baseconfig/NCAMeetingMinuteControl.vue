<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">行政办公管理</a></li>
            <li class="active">会议记录列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">会议记录列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索会议名称、会议地点" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <input type="text" class="form-control" id="created_at" placeholder="时间" />
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="MeetingMinuteTable"></table>
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
const apiUrl = '/api/nca/baseconfig/NCAMeetingMinuteControl?method=';

export default {
    data: function() {
        return {
            pagePara:{}
        }
    },
    name: 'NCAMeetingMinuteControl',
    mounted: function() {
        let _self = this;
        let $MeetingMinuteTable = $('#MeetingMinuteTable');

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 show_detail">查看</a>'
            ].join('')

        }
        window.tableEvents = {
            'click .show_detail': function(e, value, row, index) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAMeetingMinuteDetailControl',
                    query: row
                })

            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            if ($('#created_at').val()) {
                params.start_time= $('#created_at').val();
            }
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('meeting_name', '会议议题'),
                common.BTRowFormatAlignLeft('host_name', '会议主持人'),
                common.BTRowFormat('meetingroom_name', '会议地点'),
                common.BTRowFormat('start_time', '会议时间'),
                common.BTRowFormatEdSelect2Disabled(_self,'meetingroom_state', '会议室状态','meetingroomInfo'),
                common.BTRowFormatEdSelect2Disabled(_self,'host_state', '会议主持人状态','meetingroomInfo'),
                common.BTRowFormatEdSelect2Disabled(_self, 'meeting_remark_state', '状态', 'stateInfo'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $MeetingMinuteTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'meeting_id',
                uniqueId: 'meeting_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'meeting_id', $MeetingMinuteTable)
                }
            });
            common.changeTableClass($MeetingMinuteTable)
        }

        async function initData() {
            common.initDatepicker($('#created_at'));
            common.initDatepicker($('#start_time'));
            let start_time = $('#start_time').val()

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));

            common.initSelect2($('#meetingroom_state'), retData.meetingroomList);
            common.initSelect2($('#host_state'), retData.meetingroomList);
            common.initSelect2($('#meeting_state'), retData.stateInfo);

            initFitOrderTable();
            common.reSizeCall();
        }


        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#MeetingMinuteTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
textarea{
    width:100%;resize:none
}
</style>
