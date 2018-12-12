<template>
<div>
    <!--订单信息-->
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
            </div>
            <h4 class="panel-title">订单信息</h4>
        </div>
        <div class="panel-body">
            <!--省，市，区，楼盘-->
            <div class="row">
                <div data-toggle="distpicker" id="distpicker">
                    <div class="form-group col-sm-3">
                        <div class="input-group">
                            <span class="input-group-addon">省份</span>
                            <div>
                                <select class="form-control picker-width" id="province" disabled></select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-3">
                        <div class="input-group">
                            <span class="input-group-addon">市县</span>
                            <div>
                                <select class="form-control picker-width" id="city" disabled></select>
                            </div>
                        </div>
                    </div>
                    <div class="form-group col-sm-3">
                        <div class="input-group">
                            <span class="input-group-addon">区镇</span>
                            <div>
                                <select class="form-control picker-width" id="district"  @change="getEstate" disabled></select>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">小区</span>
                        <div>
                            <select class="form-control select2" id="estate" disabled></select>
                        </div>
                    </div>
                </div>
            </div>
            <!--楼栋，单元，房号，户型-->
            <div class="row">
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">楼栋</span>
                        <div>
                            <select class="form-control select2" id="build" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">单元</span>
                        <div>
                            <select class="form-control select2" id="unit" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">房号</span>
                        <div>
                            <select class="form-control select2" id="estate_room_no" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">户型</span>
                        <div>
                            <select class="form-control select2" id="order_house_type" disabled></select>
                        </div>
                    </div>
                </div>
            </div>
            <!--建筑面积，工程类型，状态，设计师-->
            <div class="row">
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">建筑面积</span>
                        <div>
                            <input class="form-control" id="order_house_area" disabled>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group">
                        <span class="input-group-addon">工程类型</span>
                        <div>
                            <select class="form-control select2" style="width:100%" id="project_type" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">状态</span>
                        <div class="">
                            <select class="form-control select2" style="width:100%" id="order_state" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">设计师</span>
                        <div class="">
                            <select class="form-control select2" style="width:100%" id="designer_id" disabled></select>
                        </div>
                    </div>
                </div>
            </div>
            <!--销售，监理，工长，房屋地址-->
            <div class="row">
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">销售</span>
                        <div class="">
                            <select class="form-control select2" style="width:100%" id="sales_id" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">监理</span>
                        <div>
                            <select class="form-contro select2" style="width:100%" id="order_supervision" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">工长</span>
                        <div>
                            <select class="form-contro select2" style="width:100%" id="order_foreman" disabled></select>
                        </div>
                    </div>
                </div>
                <div class="form-group col-sm-3">
                    <div class="input-group ">
                        <span class="input-group-addon">房屋地址</span>
                        <div>
                            <input class="form-control" id="order_address" disabled>
                        </div>
                    </div>
                </div>
            </div>
            <!--订单描述-->
            <div class="row">
                <div class="form-group col-sm-12">
                    <div class="input-group ">
                        <span class="input-group-addon">订单描述</span>
                        <div>
                            <textarea placeholder="请输入业主要求：例如背景墙，天花，木门细节需求" rows="5" class="form-control" style="resize:none;" id="order_remark" disabled></textarea>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
    <!--团队服务-->
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
            </div>
            <h4 class="panel-title">服务团队</h4>
        </div>
        <div class="panel-body">
            <table id="staffTable"></table>
        </div>
    </div>
    <!--空间列表-->
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
            </div>
            <h4 class="panel-title">空间列表</h4>
        </div>
        <div class="panel-body">
            <table id="orderRoomTable"></table>
        </div>
    </div>
    <!--订单日志-->
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
            </div>
            <h4 class="panel-title">订单日志</h4>
        </div>
        <div class="panel-body">
            <table id="historyTable"></table>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const orderApiUrl = '/api/nca/ordermanage/NCAHDOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            workRow: {},
            oldRow: '',
            fileUrl: '',
            filePath: ''
        }
    },
    name: 'NCAHDOrderBasicDetailControl',
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#order_supervision'), _self.pagePara.supervision);
            common.initSelect2($('#designer_id'), _self.pagePara.employeeInfo);
            common.initSelect2($('#order_foreman'), _self.pagePara.foreman);
            common.initSelect2($('#sales_id'), _self.pagePara.employeeInfo);
            common.initSelect2($('#materiel_checker'), _self.pagePara.employeeInfo);
            common.initSelect2($('#price_checker'), _self.pagePara.employeeInfo);
            common.initSelect2($('#change_checker'), _self.pagePara.employeeInfo);
            common.initSelect2($('#project_type'), _self.pagePara.projectTypeInfo);
            common.initSelect2($('#order_state'), _self.pagePara.orderStateInfo);
            common.initSelect2($('#room_type'), _self.pagePara.roomTypeInfo);
        },
        order: async function() {
            let _self = this;
            let $table = $('#staffTable');
            let $historyTable = $('#historyTable');
            let $orderRoomTable = $('#orderRoomTable');

            function getData() {
                _self.$http.post(orderApiUrl + 'StaffControlSRV_search', {
                    order_id: _self.order.order_id
                }).then((response) => {
                    let retdata = response.data.info
                    $table.bootstrapTable('load', {
                        data: retdata
                    })
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }

            window.tableEvents = {
                'click .order_room_delete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '空间删除', orderApiUrl + 'deleteOrderRoom', $orderRoomTable, row, 'room_id', function() {
                        _self.$parent.refreshOrder();
                    })
                }
            };

            function initTable() {
                $table.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEdSelect2Disabled(_self,'staff_type','角色','roleInfo'),
                        common.BTRowFormatAlignLeft('name', '姓名'),
                        common.BTRowFormat('staff_phone', '手机号')
                    ],
                    idField: 'id',
                    uniqueId: 'id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true
                });
                common.changeTableClass($table)
            }

            function initOrderRoomTable() {
                $orderRoomTable.bootstrapTable({
                    columns: [
                      common.BTRowFormatWithIndex('NO.'),
                      common.BTRowFormatEdSelect2Disabled(_self,'room_type','空间类型','roomTypeInfo'),
                      common.BTRowFormatAlignLeft('room_name', '空间名称'),
                    ],
                    idField: 'room_id',
                    uniqueId: 'room_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true
                });

                $orderRoomTable.bootstrapTable('load', {
                    data: _self.order.roomsInfo
                })
                $('#formA').parsley()
                common.changeTableClass($orderRoomTable)
            }

            function queryParams(params) {
                params.order_id = _self.order.order_id;
                return JSON.stringify(params);
            }

            function initHistoryTable() {
                $historyTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEdSelect2Disabled(_self,'order_state','阶段','orderStateInfo'),
                        common.BTRowFormatAlignLeft('history_event', '事件'),
                        common.BTRowFormatAlignLeft('operator_name', '操作员'),
                        common.BTRowFormat('create_date', '日期'),
                        common.BTRowFormatWithFormatterAlignLeft('history_content', '修改内容',common.remarkFormatter)
                    ],
                    idField: 'history_id',
                    uniqueId: 'history_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true,
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($historyTable)
                $historyTable.bootstrapTable('load', {
                    data: _self.order.history
                })
            }
            $('#estate').on('select2:select', async function(evt) {
                let params={
                    estate_id:common.getSelect2Val('estate')
                }
                let response = await _self.$http.post(orderApiUrl + 'getBuild', params);
                let retData = response.data.info;
                $("#build").html('');
                common.initSelect2Editable($('#build'), retData);
            });
            $('#build').on('select2:select', async function(evt) {
                let params={
                    estate_id:common.getSelect2Val('estate'),
                    build:common.getSelect2Val('build')
                }
                let response = await _self.$http.post(orderApiUrl + 'getUnit', params);
                let retData = response.data.info;
                $("#unit").html('');
                common.initSelect2Editable($('#unit'), retData);
            });
            $('#unit').on('select2:select', async function(evt) {
                let params={
                    estate_id:common.getSelect2Val('estate'),
                    build:common.getSelect2Val('build'),
                    unit:common.getSelect2Val('unit')
                }
                let response = await _self.$http.post(orderApiUrl + 'getRoomNo', params);
                let retData = response.data.info;
                $("#estate_room_no").html('');
                common.initSelect2Editable($('#estate_room_no'), retData);
            });
            $('#estate_room_no').on('select2:select', async function(evt) {
                let params={
                    room_id:common.getSelect2Val('estate_room_no')
                }
                let response = await _self.$http.post(orderApiUrl + 'getRoomType', params);
                let retData = response.data.info;
                $("#order_house_type").html('');
                $('#order_house_area').html('');
                $("#order_address").html('');

                common.initSelect2($('#order_house_type'), retData);
                $('#order_house_type').val(retData[0].id).trigger('change');
                $('#order_house_area').val(retData[0].acreage);

                let address = $('#province').val()+$('#city').val() +$('#district').val() +$("#estate option:selected").text() +$('#build').val() + '栋' +$('#unit').val() + '单元' +$("#estate_room_no option:selected").text() + '号';
                $("#order_address").val(address)
            });

            $('#project_type').val(_self.order.project_type).trigger('change');
            $('#order_state').val(_self.order.order_state).trigger('change');
            $('#order_foreman').val(_self.order.order_foreman).trigger('change');
            $('#sales_id').val(_self.order.sales_id).trigger('change');
            $('#order_supervision').val(_self.order.order_supervision ? _self.order.order_supervision : (_self.pagePara.assignment ? _self.pagePara.assignment.supervisor_id : null)).trigger('change');
            $('#designer_id').val(_self.order.designer_id ? _self.order.designer_id : (_self.pagePara.assignment ? _self.pagePara.assignment.designer_id : null)).trigger('change');
            $('#materiel_checker').val(_self.order.materiel_checker ? _self.order.materiel_checker : (_self.pagePara.assignment ? _self.pagePara.assignment.material_checker_id : null)).trigger('change');
            $('#price_checker').val(_self.order.price_checker ? _self.order.price_checker : (_self.pagePara.assignment ? _self.pagePara.assignment.price_checker_id : null)).trigger('change');
            $('#change_checker').val(_self.order.change_checker ? _self.order.change_checker : (_self.pagePara.assignment ? _self.pagePara.assignment.change_checker_id : null)).trigger('change');
            $('#order_address').val(_self.order.order_address);
            $('#order_remark').val(_self.order.order_remark);

            initTable();
            initOrderRoomTable()
            initHistoryTable();
            getData();
            this.initFormData();
            $('#historyTable').bootstrapTable("refresh", queryParams)

            $('#distpicker').distpicker({
                placeholder: true
            });

            $('#province').val(_self.order.province);
            $("#province").trigger("change");
            $('#city').val(_self.order.city);
            $("#city").trigger("change");
            $('#district').val(_self.order.district);
            await _self.getEstate();
            $('#estate').val(_self.order.estate_id).trigger('change');
            await _self.getBuild();
            $('#build').val(_self.order.build).trigger('change');
            await _self.getUnit();
            $('#unit').val(_self.order.unit).trigger('change');
            await _self.getRoomNo();
            $('#estate_room_no').val(_self.order.room_id).trigger('change');
            await _self.getaddress()
        }
    },
    methods: {
        initFormData: function () {
            let _self = this;
            $('#spaceFile').change(function() {
                let files = this.files;
                if (files.length > 0) {
                    let formData = new FormData();
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(orderApiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.fileUrl = response.data.info.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });
        },
        checkState: function(state, event) {
            let _self = this;
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        getEstate:async function(event){
            let _self = this;
            $('#estate').html('');
            let params= {
                province:$('#province').val(),
                city:$('#city').val(),
                district:$('#district').val()
            };
            let response = await _self.$http.post(orderApiUrl + 'getEstate', params);
            let retData = response.data.info;
            common.initSelect2Editable($('#estate'), retData);
        },
        getBuild:async function(event){
            let _self = this;
            let params={
                estate_id:common.getSelect2Val('estate')
            }
            let response = await _self.$http.post(orderApiUrl + 'getBuild', params);
            let retData = response.data.info;
            $("#build").html('');
            common.initSelect2Editable($('#build'), retData);
        },
        getUnit:async function(event){
            let _self = this;
            let params={
                estate_id:common.getSelect2Val('estate'),
                build:common.getSelect2Val('build')
            }
            let response = await _self.$http.post(orderApiUrl + 'getUnit', params);
            let retData = response.data.info;
            $("#unit").html('');
            common.initSelect2Editable($('#unit'), retData);
        },
        getRoomNo:async function(event){
            let _self = this;
            let params={
                estate_id:common.getSelect2Val('estate'),
                build:common.getSelect2Val('build'),
                unit:common.getSelect2Val('unit')
            }
            let response = await _self.$http.post(orderApiUrl + 'getRoomNo', params);
            let retData = response.data.info;
            $("#estate_room_no").html('');
            common.initSelect2Editable($('#estate_room_no'), retData);
        },
        getaddress:async function(event){
            let _self = this;
            let params={
                room_id:common.getSelect2Val('estate_room_no')
            }
            let response = await _self.$http.post(orderApiUrl + 'getRoomType', params);
            let retData = response.data.info;
            $("#order_house_type").html('');
            $('#order_house_area').html('');

            common.initSelect2Editable($('#order_house_type'), retData);
            if(retData && retData.length>0){
                $('#order_house_type').val(retData[0].id).trigger('change');
                $('#order_house_area').val(retData[0].acreage);
            }
        }
    }
}
</script>
<style scoped>
</style>
