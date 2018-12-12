<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">客户管理</a></li>
            <li class="active">预约列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">预约列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" placeholder="搜索姓名、手机号、地址" style="width: 200px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk" style="display:none;">
                        <ul class="nav nav-tabs">
                            <li class="active"><a href="#default-tab" @click="stateTypeSwitch(1)" data-toggle="tab">未受理</a></li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(2)" data-toggle="tab">已受理</a>
                            </li>
                            <li class=""><a href="#default-tab" @click="stateTypeSwitch(0)" data-toggle="tab">全部</a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="default-tab">
                                <table id="table"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增预约</h4>
                    </div>
                    <form @submit.prevent="addAct" id="formA">
                        <div class="modal-body">

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>客户电话</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.ap_phone" id="ap_phone" data-parsley-required="true" data-parsley-phone="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control " v-model="workRow.ap_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>预约类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="ap_type" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">建筑面积</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.ap_house_area" data-parsley-type="number">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">房屋地址</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.ap_address" maxlength="100" data-parsley-maxlength="100">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">推荐人手机</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="ap_recommender_phone" v-model="workRow.ap_recommender_phone" data-parsley-phone="true" data-parsley-group="block-1">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 6px">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" v-model="workRow.ap_remarks" maxlength="100" data-parsley-maxlength="100"></textarea>
                                    </div>
                                </div>
                            </div>
                            <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">户型</label>-->
                            <!--<div class="col-sm-8">-->
                            <!--<select class="form-control select2" id="ap_house_type" data-parsley-required="true"></select>-->
                            <!--</div>-->
                            <!--</div>-->

                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="EstateModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">受理预约</h4>
                    </div>
                    <form @submit.prevent="acceptAppointment" id="formE">
                        <div class="modal-body row">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">客户手机</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="user_phone" data-parsley-required="true" data-parsley-phone="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">客户姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="user_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">年龄</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="age"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">性别</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="gender"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">分类</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="customer_type"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">流量来源</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" style="width:100%" id="flow_source"></select>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 7px">
                                    <label class="col-sm-2 control-label">备注</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" maxlength="100" id="information_description" data-parsley-maxlength="100" placeholder="预约信息描述"></textarea>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="OrderModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">创建订单</h4>
                    </div>
                    <form @submit.prevent="createOrder" id="formO">
                        <div class="modal-body row">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">客户手机</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="user_phone2" data-parsley-required="true" data-parsley-phone="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-5">
                                    <label class="col-sm-4 control-label">客户姓名</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="user_name2" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <!--<div class="row">-->
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-4 control-label">学历</label>-->
                                    <!--<div class="col-sm-8">-->
                                        <!--<input class="form-control" id="education" maxlength="50" data-parsley-maxlength="50">-->
                                    <!--</div>-->
                                <!--</div>-->
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-4 control-label">职业</label>-->
                                    <!--<div class="col-sm-8">-->
                                        <!--<input class="form-control" id="career" maxlength="50" data-parsley-maxlength="50">-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="row">-->
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-4 control-label">收入</label>-->
                                    <!--<div class="col-sm-8">-->
                                        <!--<input class="form-control" id="income" data-parsley-type="number">-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>

        <div class="modal fade" id="ModifyModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">查看</h4>
                    </div>
                    <form @submit.prevent="modifyDm" id="formM">
                        <div class="modal-body row">
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label">性别</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2" id="gender_detail" disabled></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label">流量来源</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2" id="flow_source_detail" disabled></select>
                                </div>
                            </div>
                            <div class="col-sm-12">
                                <label class="col-sm-2 control-label">备注</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" v-model="workRow.ap_remarks" disabled></textarea>
                                </div>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/customermanage/NCAAppointmentControl?method='

    export default {
        data: function () {
            return {
                pagePara: {},
                workRow: {},
                oldRow: '',
                selectState: 1,
                orderRow: ''
            }
        },
        name: 'NCAAppointmentControl',
        mounted: function () {
            let _self = this
            let $table = $('#table')

            function houseTypeFormatter(value, row) {
                for (let i = 0; i < _self.pagePara.htypeInfo.length; i++) {
                    if (_self.pagePara.htypeInfo[i].id === value) {
                        return _self.pagePara.htypeInfo[i].text
                    }
                }
                return ''
            }

            function actFormatter(value, row) {
                if (row.ap_state === '1') {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 accept_appointment">受理预约</a>',
                        '<a class="btn btn-info btn-xs m-r-5 edit_detail">查看更多</a>',
                    ].join('')
                } else if (row.ap_state === '2') {
                    if (!row.order_id) {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 create_order">创建订单</a>'
                        ].join('')
                    } else {
                        return ''
                    }
                }
            }

            function genderInfo(value, row) {
                for (let i = 0; i < _self.pagePara.genderInfo.length; i++) {
                    if (_self.pagePara.genderInfo[i].id === value) {
                        return _self.pagePara.genderInfo[i].text
                    }
                }
                return ''
            }

            function ctypeInfo(value, row) {
                for (let i = 0; i < _self.pagePara.ctypeInfo.length; i++) {
                    if (_self.pagePara.ctypeInfo[i].id === value) {
                        return _self.pagePara.ctypeInfo[i].text
                    }
                }
                return ''
            }

            function flowSourceInfo(value, row) {
                for (let i = 0; i < _self.pagePara.flowSourceInfo.length; i++) {
                    if (_self.pagePara.flowSourceInfo[i].id === value) {
                        return _self.pagePara.flowSourceInfo[i].text
                    }
                }
                return ''
            }

            function ageInfo(value, row) {
                for (let i = 0; i < _self.pagePara.ageInfo.length; i++) {
                    if (_self.pagePara.ageInfo[i].id === value) {
                        return _self.pagePara.ageInfo[i].text
                    }
                }
                return ''
            }

            function initTable() {
                window.tableEvents = {
                    'click .accept_appointment': function (e, value, row, index) {
                        _self.oldRow = $.extend(true, {}, row);

                        $('#user_phone').val(_self.oldRow.ap_phone);
                        $('#user_name').val(_self.oldRow.ap_name);
                        $('#age').val(_self.oldRow.age).trigger('change')
                        $('#gender').val(_self.oldRow.gender).trigger('change')
                        $('#flow_source').val(_self.oldRow.flow_source).trigger('change')
                        $('#customer_type').val(_self.oldRow.customer_type).trigger('change')
                        $('#formE').parsley().reset()
                        $('#EstateModal').modal('show');
                    },
                    'click .create_order': function (e, value, row, index) {
                        _self.oldRow = $.extend(true, {}, row);
                        $('#distpicker').distpicker('reset', true);
                        $('#distpicker').distpicker('destroy', true);
                        $('#distpicker').distpicker({
                            province: "",
                            city: "",
                            district: ""
                        });
                        $('#user_phone2').val(_self.oldRow.ap_phone);
                        $('#user_name2').val(_self.oldRow.ap_name);
                        $('#education').val(_self.oldRow.education);
                        $('#career').val(_self.oldRow.career);
                        $('#income').val(_self.oldRow.income);
                        $('#estate').val(null).trigger('change')
                        $('#build').val(null).trigger('change')
                        $('#unit').val(null).trigger('change')
                        $('#room_no').val(null).trigger('change')
                        $('#order_house_type').val(null).trigger('change')
                        $('#formO').parsley().reset()
                        $('#OrderModal').modal('show');
                    },
                    'click .show_detail': function (e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    },
                    'click .edit_detail': function(e, value, row, index) {
                        _self.oldRow = $.extend(true, {}, row);
                        _self.workRow = $.extend(true, {}, row);

                        $('#gender_detail').val(row.gender).trigger('change')
                        $('#flow_source_detail').val(row.flow_source).trigger('change')
                        $('#ModifyModal').modal('show')
                    }
                }

                function orderFormatter(value, row) {
                    if (value) {
                        return [
                            '<a class="show_detail">',
                            value,
                            '</a>'
                        ].join('')
                    } else return ''
                }

                function queryParams(params) {
                    params.search_text = $('#search_text').val()
                    params.ap_state = _self.selectState.toString()
                    return JSON.stringify(params)
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('ap_name', '客户姓名'),
                        common.BTRowFormat('ap_phone', '客户手机'),
                        // common.BTRowFormatWithFormatter('gender', '性别', genderInfo),
                        // common.BTRowFormatWithFormatter('age', '年龄', ageInfo),
                        // common.BTRowFormat('education', '学历'),
                        // common.BTRowFormat('career', '职业'),
                        // common.BTRowFormat('income', '收入'),
                        // common.BTRowFormatWithFormatter('flow_source', '流量来源', flowSourceInfo),
                        common.BTRowFormatWithFormatter('customer_type', '分类', ctypeInfo),
                        common.BTRowFormatWithFormatterAlignLeft('ap_address', '装修地址',common.remarkFormatter),
                        common.BTRowFormatEdSelect2(_self, 'ap_type', '预约类型', 'projectType'),
//                    common.BTRowFormatEdSelect2(_self, 'ap_house_type', '户型', 'htypeInfo'),
                        common.BTRowFormatEditableRight('ap_house_area', '面积'),
                        common.BTRowFormatEdSelect2(_self, 'ap_state', '状态', 'apStateInfo'),
                        common.BTRowFormatWithFE('order_id', '订单号', orderFormatter, tableEvents),
                        common.BTRowFormat('ap_date', '提交日期'),
                        common.BTRowFormat('ap_recommender_phone', '推荐人手机'),
                        // common.BTRowFormatWithFormatter('ap_remarks', '备注',common.remarkFormatter),
                        common.actFormatter('act', actFormatter, tableEvents),
                    ],
                    idField: 'appoint_id',
                    uniqueId: 'appoint_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function () {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        });
                        $('[data-name="ap_state"]').each(function () {
                            let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                            if (actrow.order_id) {
                                $(this).editable({
                                    type: 'select2',
                                    emptytext: '无',
                                    source: _self.pagePara['apStateInfo'],
                                    disabled: true
                                })
                            }
                        })
                    },
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'appoint_id', $table)
                    }
                })
                common.changeTableClass($table)
            }

            function initPage() {
                _self.$http.post(apiUrl + 'init', {}).then((response) => {
                    let retData = response.data.info
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
//                common.initSelect2($('#ap_house_type'), retData.htypeInfo)
//                common.initSelect2($('#order_house_type'), retData.htypeInfo)
                    common.initSelect2($('#ap_type'), retData.projectType)
                    common.initSelect2($('#gender'), retData.genderInfo)
                    common.initSelect2($('#customer_type'), retData.ctypeInfo)
                    common.initSelect2($('#flow_source'), retData.flowSourceInfo)
                    common.initSelect2($('#gender_detail'), retData.genderInfo)
                    common.initSelect2($('#flow_source_detail'), retData.flowSourceInfo)
                    common.initSelect2($('#age'), retData.ageInfo)
                    $('#ap_phone').autocomplete({
                        minLength: 7,
                        source: function (request, response) {
                            $.ajax({
                                url: apiUrl + 'searchPhone',
                                headers: {
                                    authorization: common.getStoreData('token')
                                },
                                type: "post",
                                dataType: "json",
                                data: _self.workRow,
                                success: function (data) {
                                    let retData = data.info
                                    response($.map(data.info, function (item) {
                                        return {
                                            label: item.phone + ': ' + item.name,
                                            value: item.phone,
                                            name: item.name
                                        }
                                    }));
                                }
                            });
                        },
                        focus: function (event, ui) {
                            _self.$set(_self.workRow, 'ap_phone', ui.item.value)
                            _self.$set(_self.workRow, 'ap_name', ui.item.name)
                            return false;
                        },
                        select: function (event, ui) {
                            _self.$set(_self.workRow, 'ap_phone', ui.item.value)
                            _self.$set(_self.workRow, 'ap_name', ui.item.name)
                            return false;
                        }
                    });
                    initTable()
                    $('#formA').parsley()
                    $('#formE').parsley()
                    $('#formO').parsley()
                    common.initSelect2Editable($('#estate'), []);
                    common.initSelect2Editable($('#build'), []);
                    common.initSelect2Editable($('#unit'), []);
                    common.initSelect2Editable($('#room_no'), []);
                    common.initSelect2($('#order_house_type'), []);
                    common.reSizeCall()
                    console.log('init success')
                }, (response) => {
                    console.log('init error')
                    common.dealErrorCommon(_self, response)
                })
            }

            //从客户管理中创建的快速预约
            function quickAdd() {
                let phone = _self.$route.query.phone
                let name = _self.$route.query.name
                let province = _self.$route.query.province
                let city = _self.$route.query.city
                let district = _self.$route.query.district
                let address = _self.$route.query.address

                if (phone && name) {
                    _self.workRow = {}
                    _self.workRow.ap_phone = phone
                    _self.workRow.ap_name = name
                    _self.workRow.ap_address = province + city + district + address
//                $('#ap_house_type').val(null).trigger('change')
                    $('#formA').parsley().reset()
                    $('#AddModal').modal('show')
                }
            }

            $('#estate').on('select2:select', async function (evt) {
                let params = {
                    estate_id: common.getSelect2Val('estate')
                }
                let response = await _self.$http.post(apiUrl + 'getBuild', params);
                let retData = response.data.info;
                $("#build").html('');
                common.initSelect2Editable($('#build'), retData);
            });
            $('#build').on('select2:select', async function (evt) {

                let objRegExp;
                //栋号 3位数字
                objRegExp = /^\d{3}$/;
                if (!objRegExp.test($('#build option:selected').text())) {
                    common.dealWarningCommon('栋号必须是三位数字');
                    $("#build").val(null).trigger('change');
                    return
                }

                let params = {
                    estate_id: common.getSelect2Val('estate'),
                    build: common.getSelect2Val('build')
                }
                let response = await _self.$http.post(apiUrl + 'getUnit', params);
                let retData = response.data.info;
                $("#unit").html('');
                common.initSelect2Editable($('#unit'), retData);
            });
            $('#unit').on('select2:select', async function (evt) {
                let objRegExp;
                //单元号 2位数字，
                objRegExp = /^\d{2}$/;
                if (!objRegExp.test($('#unit option:selected').text())) {
                    common.dealWarningCommon('单元号必须是两位数字');
                    $("#unit").val(null).trigger('change');
                    return
                }
                let params = {
                    estate_id: common.getSelect2Val('estate'),
                    build: common.getSelect2Val('build'),
                    unit: common.getSelect2Val('unit')
                }
                let response = await _self.$http.post(apiUrl + 'getRoomNo', params);
                let retData = response.data.info;
                $("#room_no").html('');
                common.initSelect2Editable($('#room_no'), retData);
            });
            $('#room_no').on('select2:select', async function (evt) {
                let objRegExp;
                //单元号 4位数字，
                objRegExp = /^\d{4}$/;
                if (!objRegExp.test($('#room_no option:selected').text())) {
                    common.dealWarningCommon('房间号必须是四位数字');
                    $("#room_no").val(null).trigger('change');
                    return
                }

                let params = {
                    room_id: common.getSelect2Val('room_no')
                }
                let response = await _self.$http.post(apiUrl + 'getRoomType', params);
                let retData = response.data.info;

                $("#order_house_type").html('');
                if (retData.length == 0) {
//                let responseDefault = await _self.$http.post(apiUrl + 'init', {});
//                let retDataDefault = responseDefault.data.info;
//                common.initSelect2($('#order_house_type'), retDataDefault.htypeInfo);

                } else {
                    common.initSelect2($('#order_house_type'), retData);

                }

            });
            $(function () {
                initPage();
                quickAdd();
                $('#distpicker').distpicker({
                    placeholder: true
                });
            })
        },
        methods: {
            addM: function (event) {
                let _self = this
                _self.workRow = {}
//            $('#ap_house_type').val(null).trigger('change')
                $('#formA').parsley().reset()
                $('#AddModal').modal('show')
            },
            addAct: function (event) {
                let _self = this
                if ($('#formA').parsley().isValid()) {
//                _self.workRow.ap_house_type = common.getSelect2Val('ap_house_type')
                    _self.workRow.ap_type = common.getSelect2Val('ap_type');
                    _self.workRow.ap_recommender_phone = $('#ap_recommender_phone').val()
                    _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                        let retData = response.data.info
                        $('#table').bootstrapTable('insertRow', {
                            index: 0,
                            row: retData
                        })
                        _self.workRow = {}
                        common.dealSuccessCommon('增加成功')
//                    $('#ap_house_type').val(null).trigger('change')
                        $('#formA').parsley().reset()
                        console.log('add success')
                        $('#table').bootstrapTable("refresh", _self.queryParams)
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }

            },
            search: function (event) {
                let _self = this
                $('#table').bootstrapTable("refresh", _self.queryParams)
                console.log(_self,'7')
            },
            stateTypeSwitch: function (value) {
                let _self = this
                _self.selectState = value
                $('#table').bootstrapTable("refresh", _self.queryParams)
            },
            getEstate: async function (event) {
                try {
                    $('#estate').html('');
                    let _self = this;
                    let params = {
                        province: _self.workRow.province,
                        city: _self.workRow.city,
                        district: _self.workRow.district
                    };
                    let response = await _self.$http.post(apiUrl + 'getEstate', params);
                    let retData = response.data.info;
                    common.initSelect2Editable($('#estate'), retData);

                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }

            },
            acceptAppointment: async function () {
                let _self = this;
                try {
                    if ($('#formE').parsley().isValid()) {

                        let roomtype_id = common.getSelect2Val('order_house_type');
                        let room_id = common.getSelect2Val('room_no');

                        let params = {
                            appoint_id: _self.oldRow.appoint_id,
                            ap_address: $('#ap_address').val(),
                            roomtype_id: (roomtype_id == '') ? 0 : roomtype_id,
                            ap_estate_room_id: room_id,
                            ap_name: $('#user_name').val(),
                            ap_phone: $('#user_phone').val(),
//                          room_type: common.getSelect2Val('order_house_type')
                            flow_source: $('#flow_source option:selected').val(),
                            customer_type: $('#customer_type option:selected').val(),
                            gender: $('#gender option:selected').val(),
                            age: $('#age option:selected').val(),
                            ap_remarks: $('#information_description').val()
                        };

                        let result = await _self.$http.post(apiUrl + 'acceptAppointment', params);

                        $('#EstateModal').modal('hide');
                        $('#table').bootstrapTable("refresh", _self.queryParams);
                        $('#formE').parsley().reset()
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            },
            createOrder: async function () {
                let _self = this;
                try {
                    if ($('#formO').parsley().isValid()) {
//                        let ap_address = $('#province').val() +
//                            $('#city').val() +
//                            $('#district').val() + '-' +
//                            $('#estate option:selected').text() +
//                            $('#build option:selected').text() + '栋' +
//                            $('#unit option:selected').text() + '单元' +
//                            $('#room_no option:selected').text() + '号';
                        let ap_address;
                        let roomtype_id = common.getSelect2Val('order_house_type');
                        let room_id = common.getSelect2Val('room_no');

                        let params = {
                            appoint_id:  _self.oldRow.appoint_id,//_self.oldRow.appoint_id,
                            ap_address: ap_address,
                            roomtype_id: (roomtype_id=='')?0:roomtype_id,
                            ap_estate_room_id: room_id,
                            ap_name: $('#user_name2').val(),
                            ap_phone: $('#user_phone2').val(),
                            ap_house_area: $('#user_house_area').val(),
                            province: $('#province').val(),
                            city: $('#city').val(),
                            district: $('#district').val(),
                            estate_name: $('#estate option:selected').text(),
                            build: $('#build option:selected').text(),
                            unit: $('#unit option:selected').text(),
                            room_no: $('#room_no option:selected').text(),
//                          room_type: common.getSelect2Val('order_house_type')
                            flow_source: $('#flow_source option:selected').val(),
                            customer_type: $('#customer_type option:selected').val(),
                            gender: $('#gender option:selected').val(),
//                            education: $('#education').val(),
                            age: $('#age option:selected').val(),
//                            career:  $('#career').val(),
//                            income:  $('#income').val()
                        };

                        let result = await _self.$http.post(apiUrl + 'createOrder', params);

                        $('#OrderModal').modal('hide');
                        $('#table').bootstrapTable("refresh", _self.queryParams);
                        $('#formO').parsley().reset()
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        }
    }
</script>
<style scoped>
    .select-width {
        width: 180px;
    }
</style>
