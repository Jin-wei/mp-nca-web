<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">行政办公管理</a></li>
            <li class="active">车辆数据列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">车辆数据列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control"  id="search_text" placeholder="搜索车牌号、车辆品牌" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <!--<select class="form-control select2" multiple id="vehicle_type2">-->
                                        <!--<option value="">请选择车辆种类</option>-->
                                        <!--<option v-for="state in pagePara.vehicleType" v-bind:value="state.id">{{state.text}}</option>-->

                                    <!--</select>-->
                                    <select class="select2 form-control select-width" id="vehicle_type2" style="width: 150px;">
                                        <option value="">车辆种类</option>
                                        <option v-for="state in pagePara.vehicleType" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <!--<select class="form-control select2" multiple id="vehicle_status"></select>-->
                                    <select class="select2 form-control select-width" id="vehicle_status" style="width: 150px;">
                                        <option value="">状态</option>
                                        <option v-for="state in pagePara.vehicleStatus" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button id="search" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addMMC" class="btn btn-info" v-on:click="addMMC">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="VehicleTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddVehicleManageModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增车辆记录</h4>
                    </div>
                    <form @submit.prevent="submitMod" id="formVMC">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>车牌号</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="workRow.license_plate_num" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>车辆种类</label>
                                    <div class="col-sm-9">
                                        <select class="form-control select2" style="width:100%" id="vehicle_type" data-parsley-required="true" ></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>汽车品牌</label>
                                    <div class="col-sm-9">
                                        <input type="text" class="form-control" v-model="workRow.vehicle_brand" data-parsley-required="true" >
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="margin-bottom: 0">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>车辆管理员</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="host_id" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="form-group col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="teamP" >选择</label>
                                    </div>
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

        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAVehicleManageControl?method=';

import userSelectDialog from '../../../components/common/userSelectDialog.vue'
export default {
    data: function() {
        return {
            pagePara:{},
            pagePara2:{},
            workRow:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue()
        }
    },
    name: 'NCAVehicleControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $VehicleTable = $('#VehicleTable');

        function operateFormatter(value, row, index) {
            if(row.vehicle_status==='1'){
                return [
                    ''
                ].join('')
            }else {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 delete_meeting">删除</a>'
                ].join('')
            }

        }
        window.tableEvents = {
            'click .delete_meeting': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $VehicleTable, row, 'vehicle_id', function() {})
            }
        };

        function queryParamsFit(params) {
            params.search_text=$('#search_text').val();
            params.vehicle_type = $('#vehicle_type2').val();
            params.vehicle_status = $('#vehicle_status').val();

            return JSON.stringify(params)
        }


        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('vehicle_id', '车辆编号'),
                common.BTRowFormatEditable('license_plate_num', '车牌号'),
                common.BTRowFormatEditableLeft('vehicle_brand', '汽车品牌'),
                common.BTRowFormatEdSelect2(_self, 'vehicle_type', '车辆种类', 'vehicleType'),
                common.BTRowFormatAlignLeft('admin_user_name', '车辆管理员'),
                common.BTRowFormatEditable('vehicle_status', '状态'),
                common.BTRowFormatEdSelect2Disabled(_self,'vehicle_status_flag', '变更类型','vehicleStatusFlag'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
            ];
            $VehicleTable.bootstrapTable({
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
                idField: 'vehicle_id',
                uniqueId: 'vehicle_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody:function () {
                    $('[data-name="vehicle_status"]').each(function() {
                        let actrow = $VehicleTable.bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow && actrow.vehicle_status_flag === '1') {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true,
                                display: function(value) {
                                    let showText = ''
                                    $(_self.pagePara.vehicleStatus).each(function() {
                                        if (this.id == value) {
                                            if (this.name) {
                                                showText = this.name
                                            } else {
                                                showText = this.text
                                            }
                                            return false
                                        }
                                    })
                                    $(this).html(showText)
                                }
                            })
                        } else {
                            $(this).editable({
                                type: 'select2',
                                emptytext: '无',
                                select2: {
                                    data: _self.pagePara.vehicleStatus,
                                    width: 200
                                },
                                display: function(value) {
                                    let showText = ''
                                    $(_self.pagePara.vehicleStatus).each(function() {
                                        if (this.id == value) {
                                            if (this.name) {
                                                showText = this.name
                                            } else {
                                                showText = this.text
                                            }
                                            return false
                                        }
                                    })
                                    $(this).html(showText)
                                }
                            })
                        }
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },

                onEditableSave: async function(field, row, oldValue, $el) {
                    let response = await _self.$http.post(apiUrl + 'modify', row);
                }
            });
            common.changeTableClass($VehicleTable)
        }

        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
            let user_id = _self.pagePara.userInfo.user_id

            common.initSelect2($('#vehicle_type'), retData.vehicleType);
//            common.initSelect2Placeholder($('#vehicle_status'), retData.vehicleStatus,'状态');
//            common.initSelect2Placeholder($('#vehicle_type2'), retData.vehicleType, '车辆种类');
            initFitOrderTable();
            common.reSizeCall();
            $('#formVMC').parsley()
        }


        $(function() {
            initData();
        })
    },
    methods: {
        queryConfirm: function(event) {
            let _self = this;
            $('#VehicleTable').bootstrapTable("refresh")
        },
        addMMC: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#vehicle_type').val(null).trigger('change');
            $('#AddVehicleManageModal').modal('show');
        },
        submitMod: async function(event) {
            let _self = this;

            if ($('#formVMC').parsley().isValid()) {
                try {
                    _self.workRow.vehicle_type = common.getSelect2Val('vehicle_type');
                    _self.workRow.admin_user_id = _self.pagePara2.meetingId;

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    $('#VehicleTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formVMC').parsley().reset();
                    $('#AddVehicleManageModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        teamP: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback: async function(users) {
            let _self = this;
            try {
                _self.$http.post(apiUrl + 'changeGroup', {
                    user_id: _self.pagePara.userInfo.user_id,
                    users: users
                }).then((response) => {
                    let retData = response.data.info;
                    _self.pagePara2 = JSON.parse(JSON.stringify(retData));
                    $("#host_id").val(_self.pagePara2.meetingName)
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
textarea{
    width:100%;resize:none
}
</style>
