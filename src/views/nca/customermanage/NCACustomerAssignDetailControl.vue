<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;" @click="returnPage">总部客户查询</a></li>
        <li class="active">总部客户详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row hidedesk" style="display:none;">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <span class="panel-title">总部客户详情</span>
                    <span style="float: right" id="baseInfo"></span>
                </div>
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
                <!--<div style="padding:0px 10px 5px 0px;" align="right">-->
                    <!--<button class="btn btn-info" v-on:click="returnPage">返回</button>-->
                <!--</div>-->
            </div>
        </div>
    </div>
    <div class="modal fade" id="addContactsModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">联系记录</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-6 ">
                        <label class="col-sm-4 control-label"><span class="table-required">*</span>类型</label>
                        <div class="col-sm-8">
                            <select class="form-control select2" multiple style="width:100%" id="contact_type"> </select>
                        </div>
                    </div>
                    <div class="form-group col-sm-6 ">
                        <label class="col-sm-4 control-label"><span class="table-required">*</span>联系方式</label>
                        <div class="col-sm-8">
                            <select class="form-control select2" multiple style="width:100%" id="contact_way"> </select>
                        </div>
                    </div>
                    <div class="col-sm-12" style="padding-left: 6px">
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
                    <h4 class="modal-title">量房预约</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-12 p_r" >
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>房屋地址</label>
                        <div class="col-sm-10">
                            <input class="form-control" v-model="workRow.ap_address">
                        </div>
                    </div>
                    <div class="form-group col-sm-12 p_r">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>户型</label>
                        <div class="col-sm-6">
                            <select class="form-control select2" style="width:100%" id="ap_house_type"> </select>
                        </div>
                    </div>
                    <div class="col-sm-12 p_r">
                        <label class="col-sm-2 control-label"><span class="table-required">*</span>建筑面积</label>
                        <div class="col-sm-6">
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
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/customermanage/NCACustomerAssignControl?method=';
let customerInfo = {};

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            oldRow: ''
        }
    },
    name: 'NCACustomerAssignDetailControl',
    mounted: function() {
        let _self = this
        let $table1 = $('#table1');
        let $table2 = $('#table2');
        let $table3 = $('#table3');

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

        function initTable1() {
            $table1.bootstrapTable({
                height: 154,
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('create_date', '联系时间'),
                    common.BTRowFormatWithFormatter('contact_way', '联系方式', contactWay),
                    common.BTRowFormatWithFormatter('contact_type', '类型', contactType),
                    common.BTRowFormatAlignLeft('operator_name', '联系人'),
                    common.BTRowFormat('remark', '联系内容')
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
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify_a', row, 'appoint_id', $table2)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
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
                  /*common.BTRowFormat('final_offer', '订单金额'),*/
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

        function initPage() {
            let params = {};
            params.user_id = common.getUrlParams("userId");
            _self.$http.post(apiUrl + 'init', params).then((response) => {
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                common.initSelect2($('#contact_type'), retData.contactType);
                common.initSelect2($('#contact_way'), retData.contactWay);
                common.initSelect2($('#ap_house_type'), retData.hTypeInfo);

                if (retData.customerInfo != null && retData.customerInfo.length > 0) {
                    customerInfo = retData.customerInfo;
                    let baseInfo = customerInfo[0].name + '|' + customerInfo[0].phone + '|' + customerInfo[0].decorate_address;
                    $("#baseInfo").html(baseInfo);
                }

                initTable1();
                getData1();

                initTable2();
                initTable3();
                common.reSizeCall();
            }, (response) => {
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
            let url = '/nca/customermanage/NCACustomerAssignControl?offset=' + common.getUrlParams("offset");

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
        }
    }
}
</script>
<style scoped>
    .p_r{
        padding-right: 25px;
        padding-left: 0px;
    }
</style>
