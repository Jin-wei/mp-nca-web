<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;" @click="returnPage">C端客户管理</a></li>
        <li class="active">C端客户详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row hidedesk" style="display:none;">
        <div class="col-md-12">
            <form  @submit.prevent="saveApply" id="formApply">
                <!--交通申请-->
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <div class="row pull-right">
                                <button type="submit" class="btn btn-info btn-xs">保存</button>
                                <button type="button" v-on:click="setTask" class="btn btn-info btn-xs" style="margin-right: 5px">编辑积分</button>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning"
                                   data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                            </div>
                        </div>
                        <h4 class="panel-title">C端客户详情
                            <span id="detailInfo" style="padding-left: 250px;font-size: 14px;"></span></h4>
                    </div>
                    <div class="panel-body">
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">姓名</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="queryData.name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">手机号</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="queryData.phone" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">性别</label>
                                    <div class="col-sm-9">
                                        <select class="form-control select2"  style="width:100%" id="gender"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">地区</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="queryData.province + queryData.city + queryData.district" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">客户来源</label>
                                    <div class="col-sm-9">
                                        <select class="form-control select2"  style="width:100%" id="customer_source"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">流量来源</label>
                                    <div class="col-sm-9">
                                        <select class="form-control select2"  style="width:100%" id="flow_source"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-2 control-label" style="margin-left:-21px;">详细地址</label>
                                    <div class="col-sm-10" style="width:424px;margin-left:-2px;">
                                        <input class="form-control" v-model="queryData.decorate_address" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">状态</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="queryData.order_state" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <label class="col-sm-3 control-label">积分</label>
                                    <div class="col-sm-9">
                                        <input class="form-control" v-model="queryData.customer_point" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>
                                    </div>
                                </div>
                                <!--<div class="form-group col-sm-6">-->
                                    <!--<label class="col-sm-3 control-label">积分备注</label>-->
                                    <!--<div class="col-sm-9">-->
                                        <!--<input class="form-control" v-model="queryData.customerpoint_remarks" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" disabled>-->
                                    <!--</div>-->
                                <!--</div>-->
                            </div>
                    </div>
                </div>
            </form>
            <div class="panel panel-inverse">
                <!--<div class="panel-heading">-->
                    <!--<span class="panel-title">C端客户详情</span>-->
                    <!--<span style="float: right" id="baseInfo"></span>-->
                <!--</div>-->
                <!--<div class="panel-body">-->
                    <!--<div class="table-btn" style="float: left">客户信息</div>-->
                    <!--<div style="padding-bottom: 5px;" class="pull-right">-->
                        <!--<button class="btn btn-info" v-on:click="addappointment" style="margin-right:5px;">预约</button>-->
                        <!--<button class="btn btn-info" v-on:click="addOrder">下单</button>-->
                    <!--</div>-->
                    <!--<table id="table4"></table>-->
                <!--</div>-->
                <div class="panel-body">
                    <div class="table-btn" style="float: left">联系记录</div>
                    <div style="padding-bottom: 5px;" class="pull-right">
                        <button class="btn btn-info" v-on:click="addContacts">增加</button>
                    </div>
                    <table id="table1"></table>
                </div>
                <div class="panel-body">
                    <div class="table-btn" style="float: left">预约量房</div>
                    <div style="padding-bottom: 5px;" class="pull-right">
                        <button class="btn btn-info" v-on:click="addAppointment">增加</button>
                    </div>
                    <table id="table2"></table>
                </div>
                <div class="panel-body">
                    <div class="table-btn" style="float: left">订单记录</div>
                    <table id="table3"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="addContactsModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增联系记录</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-6">
                        <label class="col-sm-4 control-label"><span class="table-required">*</span>类型</label>
                        <div class="col-sm-8">
                            <select class="form-control select2" multiple style="width:100%" id="contact_type"> </select>
                        </div>
                    </div>
                    <div class="form-group col-sm-6">
                        <label class="col-sm-4 control-label"><span class="table-required">*</span>联系方式</label>
                        <div class="col-sm-8">
                            <select class="form-control select2" multiple style="width:100%" id="contact_way"> </select>
                        </div>
                    </div>
                    <div class="col-sm-12" style="padding-left: 5px">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>联系内容</label>
                        <div class="col-sm-10">
                            <textarea class="form-control" v-model="workRow.remark" maxlength="200" rows="5"></textarea>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info" v-on:click="saveContacts">保存</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="addAppointmentModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增预约量房记录</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>房屋地址</label>
                        <div class="col-sm-10">
                            <input class="form-control" v-model="workRow.ap_address">
                        </div>
                    </div>
                    <div class="form-group col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>户型</label>
                        <div class="col-sm-10">
                            <select class="form-control select2" style="width:100%" id="ap_house_type"> </select>
                        </div>
                    </div>
                    <div class="col-sm-12">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>建筑面积</label>
                        <div class="col-sm-10">
                            <input class="form-control" v-model="workRow.ap_house_area" placeholder="输入面积(平米)" maxlength="16" onkeyup="this.value=this.value.replace(/[^0-9.]/g,'')">
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info" v-on:click="saveAppointment">保存</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="editPointModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">编辑积分</h4>
                </div>
                <form @submit.prevent="editPoint" id="formPoint">
                    <div class="modal-body">
                        <div class="form-group">
                            <label>姓名</label>
                            <input class="form-control" type="text" v-model="queryData.name" disabled="disabled">
                        </div>
                        <div class="form-group">
                            <label>当前积分</label>
                            <input class="form-control" type="number" v-model="queryData.customer_point" disabled="disabled">
                        </div>
                        <div class="form-group">
                            <label><span class="table-required">*</span>增加积分</label>
                            <input class="form-control" type="number" v-model="pointPara.customer_add_point" data-parsley-required="true"><!--onkeyup="this.value=this.value.replace(/\D/g,'')"-->
                        </div>
                        <div class="form-group">
                            <label><span class="table-required">*</span>备注</label>
                            <textarea class="form-control" v-model="pointPara.customerpoint_remarks" maxlength="200" data-parsley-maxlength="200"
                                      data-parsley-required="true"></textarea>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">编辑</button>
                    </div>
                </form></div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/customermanage/NCACustomerControl?method=';
let customerInfo = {};

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            pointPara: {},
            oldRow: '',
            queryData: {},
            detailInfo: ''
        }
    },
    name: 'NCACustomerDetailControl',
    mounted: function() {
        let _self = this
        let $table1 = $('#table1');
        let $table2 = $('#table2');
        let $table3 = $('#table3');
        // let $table4 = $('#table4');

        _self.queryData = _self.$route.query;
        let detailInfo = _self.queryData.name + '|' + _self.queryData.phone + '|' + _self.queryData.province
            + _self.queryData.city + _self.queryData.district
        $("#detailInfo").html(detailInfo);

        function getData1() {
            let params = {};
            params.user_id = common.getUrlParams("userId");
            _self.$http.post(apiUrl + 'search_c', params).then((response) => {
                let retData = response.data.info
                $table1.bootstrapTable('load', {
                    data: retData
                })
            }, (response) => {
                // error callback
                common.dealErrorCommon(_self, response)
            })
        }

        function contactWay(value, row) {
            for (let i = 0; i < _self.pagePara.contactWay.length; i++) {
                if (_self.pagePara.contactWay[i].id === value) {
                    return _self.pagePara.contactWay[i].text
                }
            }
            return ''
        }

        function contactType(value, row) {
            for (let i = 0; i < _self.pagePara.contactType.length; i++) {
                if (_self.pagePara.contactType[i].id === value) {
                    return _self.pagePara.contactType[i].text
                }
            }
            return ''
        }

        function orderStateTypeFilter(value) {
            for (let i = 0; i < _self.pagePara.orderStateInfo.length; i++) {
                if (_self.pagePara.orderStateInfo[i].id === value) {
                    return _self.pagePara.orderStateInfo[i].text
                }
            }
            return ''
        }
        //
        // function genderInfo(value, row) {
        //     for (let i = 0; i < _self.pagePara.genderInfo.length; i++) {
        //         if (_self.pagePara.genderInfo[i].id === value) {
        //             return _self.pagePara.genderInfo[i].text
        //         }
        //     }
        //     return ''
        // }
        //
        // function zoneFormatter(value, row, index) {
        //     if (row.province) {
        //         return row.province + row.city + row.district
        //     } else {
        //         return '无'
        //     }
        // }
        //
        // function customerSourceFormatter(value, row) {
        //     for (let i = 0; i < _self.pagePara.customersourceInfo.length; i++) {
        //         if (_self.pagePara.customersourceInfo[i].id === value) {
        //             return _self.pagePara.customersourceInfo[i].text
        //         }
        //     }
        //     return ''
        // }
        //
        // function flowSourceFormatter(value, row) {
        //     for (let i = 0; i < _self.pagePara.flowSourceInfo.length; i++) {
        //         if (_self.pagePara.flowSourceInfo[i].id === value) {
        //             return _self.pagePara.flowSourceInfo[i].text
        //         }
        //     }
        //     return ''
        // }

        function initTable1() {
            $table1.bootstrapTable({
                height: 154,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('create_date', '联系时间'),
                    common.BTRowFormatWithFormatter('contact_way', '联系方式', contactWay),
                    common.BTRowFormatWithFormatter('contact_type', '类型', contactType),
                    common.BTRowFormatAlignLeft('operator_name', '联系人'),
                    common.BTRowFormatWithFormatterAlignLeft('remark', '联系内容',common.remarkFormatter)
                ],
                idField: 'customercontact_id',
                uniqueId: 'customercontact_id',
                toolbar: '#toolbar',
                clickToSelect: false,
                showRefresh: false,
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($table1)
        }

        function queryParams2(params) {
            params.user_id = common.getUrlParams("userId");
            params.offset = 0;
            params.limit = 99;
            return JSON.stringify(params);
        }

        function initTable2() {
            $table2.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_a',
                queryParams: queryParams2,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: 154,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('appoint_id', '预约单号'),
                    common.BTRowFormatWithFormatterAlignLeft('ap_address', '房屋地址',common.remarkFormatter),
                    common.BTRowFormatEdSelectAlignLeft(_self, 'ap_house_type', '户型', 'hTypeInfo'),
                    common.BTRowFormatEditableRight('ap_house_area', '建筑面积'),
                    common.BTRowFormatEdSelect2(_self, 'ap_state', '状态', 'apStateInfo'),
                    common.BTRowFormat('ap_date', '提交时间')
                ],
                idField: 'appoint_id',
                uniqueId: 'appoint_id',
                //                    toolbar: '#toolbar',
                clickToSelect: true,
                showRefresh: false,
                pagination: false,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_a', row, 'appoint_id', $table2)
                }
            })
            common.changeTableClass($table2)
        }

        function queryParams3(params) {
            params.user_id = common.getUrlParams("userId");
            params.offset = 0;
            params.limit = 99;
            return JSON.stringify(params);
        }

        function initTable3() {
            $table3.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_w',
                queryParams: queryParams3,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: 154,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('order_id', '订单号'),
                    common.BTRowFormatWithFormatterAlignRight('final_offer', '订单金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignLeft('order_remark', '订单描述',common.remarkFormatter),
                    common.BTRowFormat('create_date', '登记日期'),
                    common.BTRowFormat('break_date_f', '施工日期'),
                    common.BTRowFormatWithFormatter('order_state', '状态', orderStateTypeFilter),
//                    common.BTRowFormat('order_state', '状态'),
                    common.BTRowFormatAlignLeft('order_salesman', '业务员'),
                    common.BTRowFormatAlignLeft('order_designer', '设计师'),
                    common.BTRowFormatAlignLeft('order_supervision', '施工监理')
                ],
                idField: 'order_id',
                uniqueId: 'order_id',
                //                    toolbar: '#toolbar',
                clickToSelect: true,
                showRefresh: false,
                pagination: false,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($table3)
        }

        // function actFormatter(value, row) {
        //     return [
        //         '<a class="btn btn-info btn-xs m-r-5 edit_point btn-info-table">编辑积分</a>'
        //     ].join('')
        // }
        //
        // function nameFormatter(value, row) {
        //     return [
        //         '<a class="show_detail" style="cursor: pointer" title="查看">',
        //         value,
        //         '</a>'
        //     ].join('')
        // }
        //
        // function queryParams4(params) {
        //     params.user_id = common.getUrlParams("userId");
        //     params.offset = 0;
        //     params.limit = 99;
        //     return JSON.stringify(params);
        // }

        // function initTable4() {
        //     window.tableEvents = {
        //         'click .edit_point':function (e, value, row, index) {
        //             _self.pointPara=row;
        //             $('#formPoint').parsley().reset();
        //             $('#editPointModal').modal('show');
        //         }
        //     }
        //
        //     $table4.bootstrapTable({
        //         method: 'POST',
        //         url: apiUrl + 'search_p',
        //         queryParams: queryParams4,
        //         sidePagination: 'server',
        //         ajaxOptions: common.bootstrapTableAjaxOtions,
        //         responseHandler: function(res) {
        //             return res.info;
        //         },
        //         height: 154,
        //         columns: [
        //             common.BTRowFormatWithIndex('NO.'),
        //             common.BTRowFormat('phone', '手机号'),
        //             common.BTRowFormatAlignLeft('name', '姓名'),
        //             common.BTRowFormatEdSelect2Disabled(_self, 'gender', '性别', 'genderInfo'),
        //             common.BTRowFormatWithFormatter('', '地区', zoneFormatter),
        //             common.BTRowFormatWithFormatterAlignLeft('decorate_address', '详细地址',common.remarkFormatter),
        //             common.BTRowFormatWithFormatter('customer_source', '客户来源', customerSourceFormatter),
        //             common.BTRowFormatWithFormatter('flow_source', '流量来源', flowSourceFormatter),
        //             common.BTRowFormatAlignRight('customer_point', '积分'),
        //             common.BTRowFormatWithFormatterAlignLeft('customerpoint_remarks', '备注',common.remarkFormatter),
        //             common.actFormatter('act', actFormatter, tableEvents)
        //         ],
        //         idField: 'user_id',
        //         uniqueId: 'user_id',
        //         //                    toolbar: '#toolbar',
        //         clickToSelect: true,
        //         showRefresh: false,
        //         pagination: false,
        //         pageSize:common.pageSize(),
        //         pageList: [10, 15, 25, 50, 100],
        //         locale: 'zh-CN',
        //         onPostBody: function() {
        //             $('[data-toggle="popover"]').each(function () {
        //                 $(this).popover()
        //             })
        //         }
        //     })
        //     common.changeTableClass($table4)
        // }

        function initPage() {
            let params = {};
            params.user_id = common.getUrlParams("userId");
            _self.$http.post(apiUrl + 'init', params).then((response) => {
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                common.initSelect2($('#contact_type'), retData.contactType);
                common.initSelect2($('#contact_way'), retData.contactWay);
                common.initSelect2($('#ap_house_type'), retData.hTypeInfo);
                common.initSelect2($('#gender'), retData.genderInfo);
                common.initSelect2($('#customer_source'), retData.customersourceInfo);
                common.initSelect2($('#flow_source'), retData.flowSourceInfo);
                _self.queryData.order_state = _self.pagePara.detailList[0].order_state

                if (retData.customerInfo != null && retData.customerInfo.length > 0) {
                    customerInfo = retData.customerInfo;
                    let baseInfo = customerInfo[0].name + '|' + customerInfo[0].phone + '|' + customerInfo[0].decorate_address;
                    $("#baseInfo").html(baseInfo);
                }
                $('#gender').val(_self.queryData.gender).trigger('change');
                $('#customer_source').val(_self.queryData.customer_source).trigger('change');
                $('#flow_source').val(_self.queryData.flow_source).trigger('change');
                initTable1();
                getData1();


                initTable2();
                initTable3();
                // initTable4();
                common.reSizeCall();
                console.log('init success')
            }, (response) => {
                console.log('init error')
                common.dealErrorCommon(_self, response)
            })

        }

        function quickAddContact () {
          let phone = _self.$route.query.phone
          if (phone){
            _self.workRow = {}
            $('#contact_type').val(null).trigger('change');
            $('#contact_way').val(null).trigger('change');
            $('#addContactsModal').modal('show')
          }
        }

        $(function() {
            initPage()
            window.setTimeout(function () {
                quickAddContact()
            },1000)
        })
    },
    methods: {
        addContacts: function(event) {
            let _self = this
            _self.workRow = {}
            $('#contact_type').val(null).trigger('change');
            $('#contact_way').val(null).trigger('change');
            $('#addContactsModal').modal('show')
        },
        saveContacts: function(event) {
            let _self = this;
            if (!_self.workRow.remark) {
                common.dealPromptCommon('请填写联系内容')
                return
            }

            if (!common.selectCheck(_self, 'contact_type', '请选择类型')) return;
            if (!common.selectCheck(_self, 'contact_way', '请选择联系方式')) return;

            let operatorId = common.getStoreData('userinfo').user_id;
            let operatorName = common.getStoreData('userinfo').name;

            _self.workRow.user_id = common.getUrlParams("userId");
            _self.workRow.operatorId = operatorId;
            _self.workRow.operatorName = operatorName;
            _self.$http.post(apiUrl + 'add_c', _self.workRow).then((response) => {
                let retData = response.data.info
                $('#table1').bootstrapTable('insertRow', {
                    index: 0,
                    row: retData
                })
                _self.workRow = {}
                console.log('add success')
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        addAppointment: function() {
            let _self = this;
            _self.workRow = {};
            _self.workRow.ap_address = customerInfo[0].decorate_address;
            $('#addAppointmentModal').modal('show')
        },
        saveAppointment: function(event) {
            let _self = this;
            if (!_self.workRow.ap_address) {
                common.dealPromptCommon('请填写房屋地址')
                return
            }
            if (!_self.workRow.ap_house_area) {
                common.dealPromptCommon('请填写建筑面积')
                return
            }
            if (isNaN(_self.workRow.ap_house_area)) {
                common.dealPromptCommon('建筑面积请输入数字')
                return
            }

            if (!common.selectCheck(_self, 'ap_house_type', '请选择户型')) return

            _self.workRow.ap_house_type = $('#ap_house_type').val()[0];
            _self.workRow.user_id = common.getUrlParams("userId");
            _self.workRow.ap_name = customerInfo[0].name;
            _self.workRow.ap_phone = customerInfo[0].phone;
            _self.workRow.ap_remarks = "";

            _self.$http.post(apiUrl + 'add_a', _self.workRow).then((response) => {
                let retData = response.data.info;
                $('#table2').bootstrapTable('insertRow', {
                    index: 0,
                    row: retData
                })
                _self.workRow = {}
                common.dealSuccessCommon('增加成功')
                console.log('add success')
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        returnPage: function(event) {
            let _self = this;
            let url = '/nca/customermanage/NCACustomerControl?offset=' + common.getUrlParams("offset");

            if (common.getUrlParams("customer_level") != null) {
                url += '&customer_level=' + common.getUrlParams("customer_level")
            }
            if (common.getUrlParams("customer_type") != null) {
                url += '&customer_type=' + common.getUrlParams("customer_type")
            }
            if (common.getUrlParams("customer_state") != null) {
                url += '&customer_state=' + common.getUrlParams("customer_state")
            }
            if (common.getUrlParams("search_text") != null) {
                url += '&search_text=' + common.getUrlParams("search_text")
            }
            this.$router.push({
                path: url
            })
        },
        editPoint:async function () {
            let _self=this;
            try{
                if ($('#formPoint').parsley().isValid()) {
                    console.log('_self.pointPara.customer_add_point',_self.pointPara.customer_add_point)
                    if(_self.pointPara.customer_add_point<0){
                        if(Math.abs(_self.pointPara.customer_add_point)>_self.pointPara.customer_point){
                            return common.dealWarningCommon("积分减少值不能大于当前积分！");
                        }
                    }
                    _self.pointPara.user_id = common.getUrlParams("userId");
                    let params=_self.pointPara;
                    let response=await _self.$http.post(apiUrl + 'modify_point',params);
                    let retData = response.data.info;
                    if(response.data.errno==0){
                        _self.queryData.customer_point = retData.customer_point;
                        $('#editPointModal').modal('hide');
                        // $('#table4').bootstrapTable("refresh")
                    }
                }
            }catch (error){
                common.dealErrorCommon(_self, error);
            }

        },
        // addappointment:async function () {
        //     let _self=this;
        //     try{
        //         let customer_state = common.getUrlParams("customer_state");
        //         let decorate_address = _self.$route.query.decorate_address;
        //         if(customer_state === "0"){
        //             common.dealPromptCommon('该用户已停用，不可预约')
        //         }else{
        //             if (decorate_address == null) {
        //                 decorate_address = ''
        //             }
        //             _self.$router.push({
        //                 path: 'NCAAppointmentControl',
        //                 query: {
        //                     phone: _self.$route.query.phone,
        //                     name: _self.$route.query.name,
        //                     province: _self.$route.query.province,
        //                     city: _self.$route.query.city,
        //                     district: _self.$route.query.district,
        //                     address: decorate_address
        //                 }
        //             })
        //         }
        //     }catch (error){
        //         common.dealErrorCommon(_self, error);
        //     }
        //
        // },
        // addOrder:async function () {
        //     let _self=this;
        //     try{
        //         _self.$router.push({
        //             path: '/nca/ordermanage/NCAOrderControl',
        //             query: {
        //                 phone: _self.$route.query.phone,
        //                 name: _self.$route.query.name,
        //             }
        //         })
        //     }catch (error){
        //         common.dealErrorCommon(_self, error);
        //     }
        //
        // },
        saveApply: async function(){
            let _self=this;

            _self.queryData.gender= common.getSelect2Val('gender');
            _self.queryData.customer_source= common.getSelect2Val('customer_source');
            _self.queryData.flow_source= common.getSelect2Val('flow_source');
            _self.queryData.user_id = common.getUrlParams("userId");
            let params=_self.queryData;
            
            _self.$http.post(apiUrl + 'modify_detail', params).then((response) => {
                if(response.body.errno==0){
                    common.dealSuccessCommon('保存成功');
                }
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },
        setTask:async function () {
            let _self=this;
            _self.pointPara = {}
            $('#formPoint').parsley().reset();
            $('#editPointModal').modal('show');
        }
    }
}
</script>
<style scoped>
</style>
