<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">会议室数据维护</a></li>
            <li class="active">会议室详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button type="button" class="btn btn-info btn-xs" @click="addM">新增设备记录</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">会议室详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="row" style="margin-top: 10px">
                            <div class="form-group col-sm-3">
                                <label class="col-sm-6 control-label">会议室编号</label>
                                <div class="col-sm-6">
                                    <input class="form-control" v-model="queryData.meetingroom_id" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-3">
                                <label class="col-sm-6 control-label">会议室名称</label>
                                <div class="col-sm-6">
                                    <input class="form-control" v-model="queryData.meetingroom_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-3">
                                <label class="col-sm-6 control-label">管理员</label>
                                <div class="col-sm-6">
                                    <input class="form-control" v-model="queryData.meetinguser_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body ">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="meetingRoomDetailTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddmeetingRoomDetailModal">
            <div class="modal-dialog" style="width: 1100px;min-height: 400px">
                <div class="modal-content" style="min-height: 400px">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增设备记录</h4>
                    </div>
                    <div class="modal-body">
                        <div class="form-group col-xs-3">
                            <input class="form-control"  id="searchMeetingName" placeholder="搜索设备编码、名称、规格型号" v-on:input="getMeetingS">
                        </div>
                        <div class="form-group col-xs-3">
                            <button type="button" class="btn btn-info" v-on:click="addMeetingApplyDetail">保存</button>
                        </div>
                        <div class="panel-body auto-height">
                            <div class="btn-group col-md-10 no-padding" style="margin-bottom: 15px">
                                <button type="button" class="btn btn-white" v-for="model in pagePara.AssetsClassification" v-bind:class="{active: selectModelType == model.id}" @click="switchModel(model.id)">{{model.text}}</button>
                            </div>
                            <table id="meetingTableSearch"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMeetingRoomManageControl?method=';

export default {
    data: function() {
        return {
            pagePara:{},
            workRow:{},
            queryData: {},
            searchedRow:[],
            meetingroom_id: '',
            selectModelType: 1 //1固定 2低值
        }
    },
    name: 'NCAMeetingRoomDetailControl',
    mounted: function() {
        let _self = this;
        let $meetingRoomDetailTable = $('#meetingRoomDetailTable');
        let $meetingTableSearch = $('#meetingTableSearch');
        _self.queryData = _self.$route.query;

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 meetingDetail_delete">删除</a>'
            ].join('')
        }
        window.tableEvents = {
            'click .meetingDetail_delete': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_e', $meetingRoomDetailTable, row, 'meetingroomequipment_id', function() {})
            }
        };

        function queryParamsFit(params) {
            params.meetingroom_id = _self.queryData.meetingroom_id;
            return JSON.stringify(params)
        }
        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('equipment_name', '设备名称'),
                common.BTRowFormat('equipment_unit', '单位'),
                // common.BTRowFormat('equipment_num', '数量'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $meetingRoomDetailTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_e',
                queryParams: queryParamsFit,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columsArr,
                idField: 'meetingroomequipment_id',
                uniqueId: 'meetingroomequipment_id',
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
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'meetingroomequipment_id', $meetingRoomDetailTable)
                }
            });
            common.changeTableClass($meetingRoomDetailTable)
        }

        function queryParamsMeeting(params) {
            params.searchMeetingName = $('#searchMeetingName').val();
            if ( _self.selectModelType == 1 ) {
                params.assets_type = 1
            } else {
                params.assets_type = 2
            }
            return JSON.stringify(params)
        }
        function initTableAssets() {
            $meetingTableSearch.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMeeting',
                queryParams: queryParamsMeeting,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    {
                        field: 'state',
                        checkbox: true
                    },
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('fixedassets_no', '固定资产编号'),
                    common.BTRowFormatAlignLeft('fixedassets_name', '固定资产名称'),
                    common.BTRowFormat('fixedassets_model', '规格型号'),
                    common.BTRowFormat('fixedassets_unit', '计量单位'),
                    common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                    common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                    common.BTRowFormat('consumables_specifications', '规格型号'),
                    common.BTRowFormat('consumables_unit', '计量单位')
                    // common.BTRowFormatEnumberWMin('apply_amount', '默认数量'),
                ],
                idField: 'fixedassets_id',
                uniqueId: 'fixedassets_id',
                clickToSelect: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10,15,25,50,100],
                locale: 'zh-CN',
                onCheckAll: function(rows) {
                    for (let i = 0; i < rows.length; i++) {
                        _self.searchedRow.push(rows[i])
                    }
                },
                onUncheckAll: function(rows) {
                    _self.searchedRow = []
                },
                onCheck: function(row, $element) {
                    _self.searchedRow.push(row)
                },
                onUncheck: function(row, $element) {
                    _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                }
            });
            common.changeTableClass($meetingTableSearch)
        }

        async function initData() {

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            _self.AssetsClassification = _self.pagePara.AssetsClassification[0].id;
            _self.meetingroom_id = common.getUrlParams('meetingroom_id');
            initFitOrderTable();
            initTableAssets();
            common.reSizeCall();
            $('#formMRD').parsley()
        }

        $(function() {
            initData();
        })
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#AddmeetingRoomDetailModal').modal('show');
            if(_self.selectModelType == 1) {//固定
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_no');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_name');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_model');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_unit');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_detail_code');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_name');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_specifications');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_unit');
            } else if (_self.selectModelType == 2) {//低值
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_detail_code');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_name');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_specifications');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_unit');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_no');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_name');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_model');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_unit');
            }
            _self.searchedRow = [];
            $("#searchMeetingName").val('');
            $('#meetingTableSearch').bootstrapTable("refresh");
            $("input[type=checkbox]").each(function() {
                $(this).prop("checked", false);
            })
        },
        getMeetingS:function(event){
            let _self = this;
            $('#meetingTableSearch').bootstrapTable("refresh")
        },
        addMeetingApplyDetail:function(event){
            let _self = this;
            let params=[];
            if (_self.selectModelType == 1) {
                for(let s of _self.searchedRow){
                    params.push({
                        meetingroom_id:_self.meetingroom_id,
                        assets_id:s.fixedassets_no,
                        equipment_name:s.fixedassets_name,
                        equipment_unit:s.fixedassets_unit
                    })
                }
            } else {
                for(let s of _self.searchedRow){
                    params.push({
                        meetingroom_id:_self.meetingroom_id,
                        assets_id:s.consumables_detail_code,
                        equipment_name:s.consumables_name,
                        equipment_unit:s.consumables_unit
                    })
                }
            }
            if (params.length==0){
                return common.dealPromptCommon('请选择设备')
            }
            _self.$http.post(apiUrl + 'add_e', params).then((response) => {
                let retData = response.data.info;
                $('#meetingRoomDetailTable').bootstrapTable("refresh");
                _self.searchedRow=[];
                $('#AddmeetingRoomDetailModal').modal('hide');
                common.dealSuccessCommon('保存成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },
        switchModel: function (index) {
            let _self = this
            _self.selectModelType = index;
            if(index == 1) {//固定
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_no');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_name');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_model');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'fixedassets_unit');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_detail_code');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_name');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_specifications');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'consumables_unit');
            } else if (index == 2) {//低值
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_detail_code');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_name');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_specifications');
                $('#meetingTableSearch').bootstrapTable('showColumn', 'consumables_unit');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_no');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_name');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_model');
                $('#meetingTableSearch').bootstrapTable('hideColumn', 'fixedassets_unit');
            }
            $('#meetingTableSearch').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
textarea{
    width:100%;resize:none
}
</style>
