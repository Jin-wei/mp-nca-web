<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">客户管理</a></li>
        <li class="active">C端客户列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">C端客户列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" multiple v-model="workRow.search_text" id="search_text" placeholder="搜索姓名、手机号、地址" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="search_customer_type">
                                      <option value="">请选择分类</option>
                                      <option v-for="type in pagePara.ctypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                  </select>
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="search_customer_state">
                                      <option value="">请选择状态</option>
                                      <option v-for="state in pagePara.cstateInfo" v-bind:value="state.id">{{state.text}}</option>
                                  </select>
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
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
                        <li class="active"><a href="#default-tab" @click="customerleveleSwitch(3)" data-toggle="tab">潜在客户</a></li>
                        <li class=""><a href="#default-tab" @click="customerleveleSwitch(1)" data-toggle="tab">意向客户</a></li>
                        <li class=""><a href="#default-tab" @click="customerleveleSwitch(2)" data-toggle="tab">成交客户</a></li>
                        <li class=""><a href="#default-tab" @click="customerleveleSwitch(0)" data-toggle="tab">全部客户</a></li>
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
                    <h4 class="modal-title">客户信息</h4>
                </div>
                <form @submit.prevent="submitMod" id="formMod">
                    <div class="modal-body row">
                        <div class="form-group col-sm-6 p_l">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>姓名</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label"><span class="table-required">*</span>手机号</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.phone" data-parsley-required="true" data-parsley-phone="true">
                            </div>
                        </div>
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">学历</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<input class="form-control" v-model="workRow.education" maxlength="50" data-parsley-maxlength="50">-->
                            <!--</div>-->
                        <!--</div>-->
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">年龄</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<select class="form-control select2" id="age" style="width:100%"></select>-->
                            <!--</div>-->
                        <!--</div>-->
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">职业</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<input class="form-control" v-model="workRow.career" maxlength="50" data-parsley-maxlength="50">-->
                            <!--</div>-->
                        <!--</div>-->
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">收入</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<input class="form-control" v-model="workRow.income" data-parsley-type="number">-->
                            <!--</div>-->
                        <!--</div>-->
                        <div class="form-group col-sm-6 p_l">
                            <label class="col-sm-4 control-label">性别</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" style="width:100%" id="gender"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">级别</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" style="width:100%" id="customer_level"></select>
                            </div>
                        </div>
                        <!--<div class="form-group col-sm-6">-->
                            <!--<label class="col-sm-4 control-label">分类</label>-->
                            <!--<div class="col-sm-8">-->
                                <!--<select class="form-control select2" style="width:100%" id="customer_type"></select>-->
                            <!--</div>-->
                        <!--</div>-->
                        <div class="form-group col-sm-6 p_l">
                            <label class="col-sm-4 control-label">流量来源</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" style="width:100%" id="flow_source"></select>
                            </div>
                        </div>
                        <div class="row margin_left">
                        <div class="form-group col-sm-6" v-show="modifyFlag==1">
                            <label class="col-sm-4 control-label">状态</label>
                            <div class="col-sm-8" style="width:183.33px;">
                                <select class="form-control select2" style="width:100%" id="customer_state"> </select>
                            </div>
                        </div>
                        </div>
                        <div class="row m_r" style="padding-bottom: 15px">
                            <div data-toggle="distpicker" id="distpicker">
                                <div class="col-sm-4 p_l" style="margin-left: 34px">
                                    <span class="col-sm-4 control-label font-style">省</span>
                                    <div class="col-sm-7">
                                        <select class="form-control picker-width" id="province"></select>
                                    </div>
                                </div>
                                <div class="col-sm-4 m_l">
                                    <span class="col-sm-4 control-label font-style">市/县</span>
                                    <div class="col-sm-5">
                                        <select class="form-control picker-width" id="city"></select>
                                    </div>
                                </div>
                                <div class="col-sm-4 m_l">
                                    <span class="col-sm-3 control-label font-style">区</span>
                                    <div class="col-sm-9">
                                        <select class="form-control picker-width" id="district"></select>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="col-sm-12 m_p p_l" style="margin-left: 33px">
                            <div class="form-group col-sm-10">
                                <label class="address-label">详细地址</label>
                                <div class="address-input">
                                    <input class="form-control" v-model="workRow.decorate_address" maxlength="100" data-parsley-maxlength="100">
                                </div>
                            </div>
                        </div>

                        <div class="col-sm-12 m_p" style="margin-left: 23px">
                            <label class="remark-label">备注</label>
                            <div class="remark-input">
                                <textarea class="form-control" v-model="workRow.customer_remarks" maxlength="200" data-parsley-maxlength="200"></textarea>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" @click="modFlag='1'" id="addAct">保存</button>
                        <button type="submit" class="btn btn-info" @click="modFlag='2'" id="modAct">编辑</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <!--积分modal-->
    <!--<div class="modal fade" id="editPointModal">-->
        <!--<div class="modal-dialog">-->
            <!--<div class="modal-content">-->
                <!--<div class="modal-header">-->
                    <!--<button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>-->
                    <!--<h4 class="modal-title">编辑积分</h4>-->
                <!--</div>-->
                <!--<form @submit.prevent="editPoint" id="formPoint">-->
                    <!--<div class="modal-body">-->
                        <!--<div class="form-group">-->
                            <!--<label>姓名</label>-->
                            <!--<input class="form-control" type="text" v-model="pointPara.name" disabled="disabled">-->
                        <!--</div>-->
                        <!--<div class="form-group">-->
                            <!--<label>当前积分</label>-->
                            <!--<input class="form-control" type="number" v-model="pointPara.customer_point" disabled="disabled">-->
                        <!--</div>-->
                        <!--<div class="form-group">-->
                            <!--<label>增加积分</label>-->
                            <!--<input class="form-control" type="number" v-model="pointPara.customer_add_point" onkeyup="this.value=this.value.replace(/\D/g,'')"    data-parsley-required="true">-->
                        <!--</div>-->
                        <!--<div class="form-group">-->
                            <!--<label>备注</label>-->
                            <!--<textarea class="form-control" v-model="pointPara.customerpoint_remarks" maxlength="200" data-parsley-maxlength="200"-->
                                     <!--data-parsley-required="true"></textarea>-->
                        <!--</div>-->
                    <!--</div>-->
                    <!--<div class="modal-footer">-->
                        <!--<button type="submit" class="btn btn-primary btn-info"><i class="fa fa-fw fa-pencil "></i>编辑</button>-->
                    <!--</div>-->
                <!--</form></div>-->
        <!--</div>-->
    <!--</div>-->
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/customermanage/NCACustomerControl?method='
//详情页返回的offset
let tableOffset1 = 0;
//当前页的offset
let tableOffset2 = 0;
//table当前页数
let nowPage = 1;
//table每页显示条数
const pageSize = 10;
let domain_id = 0;

let customer_level;

function queryParams(params) {
    //从其他页面返回，列表显示跳转前页码
    if (tableOffset1 != 0) {
        params.offset = tableOffset1;
        if (common.getUrlParams("customer_type") != null) {
            $('#search_customer_type').val(common.getUrlParams("customer_type"))
        }
        if (common.getUrlParams("customer_state") != null) {
            $('#search_customer_state').val(common.getUrlParams("customer_state"))
        }
        if (common.getUrlParams("search_text") != null) {
            $('#search_text').val(common.getUrlParams("search_text"))
        }
        tableOffset1 = 0;
    }

    tableOffset2 = params.offset;
    params.domain_id = domain_id;
//  params.customer_level = $('#search_customer_level').val() ? $('#search_customer_level').val()[0] : ''
    params.customer_level = customer_level
    params.customer_type = $('#search_customer_type').val() ? $('#search_customer_type').val()[0] : ''
    params.customer_state = $('#search_customer_state').val() ? $('#search_customer_state').val() : ''
    params.search_text = $('#search_text').val();
    return JSON.stringify(params)
}

function isSpecialCity(city) {
    let citys = ['东莞市', '中山市', '三沙市', '儋州市', '嘉峪关市']
    let index = $.inArray(city, citys)
    return (index != -1 ? true : false)
}

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            pointPara:{},
            oldRow: '',
            modifyFlag: 0,
            modFlag: ''
        }
    },
    name: 'NCACustomerControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')

        function customerSourceFormatter(value, row) {
            for (let i = 0; i < _self.pagePara.customersourceInfo.length; i++) {
                if (_self.pagePara.customersourceInfo[i].id === value) {
                    return _self.pagePara.customersourceInfo[i].text
                }
            }
            return ''
        }

        function flowSourceFormatter(value, row) {
            for (let i = 0; i < _self.pagePara.flowSourceInfo.length; i++) {
                if (_self.pagePara.flowSourceInfo[i].id === value) {
                    return _self.pagePara.flowSourceInfo[i].text
                }
            }
            return ''
        }

        function actFormatter(value, row) {
            return [
                '<a class="btn btn-info btn-xs m-r-5 edit_detail">编辑</a>',
                // '<a class="btn btn-info btn-xs m-r-5 appointment">预约</a>',
                // '<a class="btn btn-info btn-xs m-r-5 order">下单</a>',
                '<a class="btn btn-info btn-xs m-r-5 track">跟进</a>',
                // '<a class="btn btn-primary btn-xs m-r-5 edit_point btn-info-table">编辑积分</a>'
            ].join('')
        }

        function nameFormatter(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }

        function zoneFormatter(value, row, index) {
            if (row.province) {
                /*return row.province + row.city + row.district*/
                return common.remarkFormatter(row.province + row.city + row.district)
            } else {
                return '无'
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

        function clevelInfo(value, row) {
            for (let i = 0; i < _self.pagePara.clevelInfo.length; i++) {
                if (_self.pagePara.clevelInfo[i].id === value) {
                    return _self.pagePara.clevelInfo[i].text
                }
            }
            return ''
        }

        function cstateInfo(value, row) {
            for (let i = 0; i < _self.pagePara.cstateInfo.length; i++) {
                if (_self.pagePara.cstateInfo[i].id === value) {
                    return _self.pagePara.cstateInfo[i].text
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
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row)
                    _self.workRow = $.extend(true, {}, row)
                    _self.modifyFlag = 1;
                    $('#customer_state').val(_self.workRow.customer_state).trigger('change')
                    $('#customer_level').val(_self.workRow.customer_level).trigger('change')
                    $('#customer_type').val(_self.workRow.customer_type).trigger('change')
                    $('#gender').val(_self.workRow.gender).trigger('change')
                    // $('#age').val(_self.workRow.age).trigger('change')
                    $('#flow_source').val(_self.workRow.flow_source).trigger('change')
                    $('#distpicker').distpicker('reset', true);
                    $('#distpicker').distpicker('destroy', true);
                    $('#distpicker').distpicker({
                        province: _self.workRow.province,
                        city: _self.workRow.city,
                        district: _self.workRow.district
                    });
                    $('#modAct').show()
                    $('#addAct').hide()
                    $('#formMod').parsley().reset()
                    $('#AddModal').modal('show')
                },
                'click .show_detail': function(e, value, row, index) {
                    let url = '/nca/customermanage/NCACustomerDetailControl?userId=' + row.user_id + '&offset=' + tableOffset2
                        + '&phone=' + row.phone + '&name=' + row.name + '&province=' + row.province + '&city='+ row.city +'&district=' +
                        row.district + '&decorate_address=' + row.decorate_address + '&customer_state=' + row.customer_state + '&gender=' + row.gender
                        + '&customer_source=' + row.customer_source + '&region=' + row.province + row.city + row.district +
                        '&flow_source=' + row.flow_source+ '&customer_point=' + row.customer_point +
                        '&customerpoint_remarks=' + row.customerpoint_remarks;
                    if (_self.workRow.customer_state != null && _self.workRow.customer_state != "") {
                        url += '&customer_state=' + _self.workRow.customer_state
                    }
                    if (_self.workRow.customer_level != null && _self.workRow.customer_level != "") {
                        url += '&customer_level=' + _self.workRow.customer_level
                    }
                    if (_self.workRow.customer_type != null && _self.workRow.customer_type != "") {
                        url += '&customer_type=' + _self.workRow.customer_type
                    }
                    if (_self.workRow.search_text != null && _self.workRow.search_text != "") {
                        url += '&search_text=' + _self.workRow.search_text
                    }
                    _self.$router.push({
                        path: url
                    })
                },
                // 'click .appointment': function(e, value, row, index) {
                //     if(row.customer_state === "0"){
                //         common.dealPromptCommon('该用户已停用，不可预约')
                //     }else{
                //         _self.$router.push({
                //             path: 'NCAAppointmentControl',
                //             query: {
                //                 phone: row.phone,
                //                 name: row.name,
                //                 province: row.province,
                //                 city: row.city,
                //                 district: row.district,
                //                 address: row.decorate_address
                //             }
                //         })
                //     }
                //
                // },
                // 'click .order': function(e, value, row, index) {
                //     _self.$router.push({
                //         path: '/nca/ordermanage/NCAOrderControl',
                //         query: {
                //             phone: row.phone,
                //             name: row.name,
                //         }
                //     })
                // },
                'click .track': function(e, value, row, index) {
                    let url = '/nca/customermanage/NCACustomerDetailControl?userId=' + row.user_id + '&offset=' + tableOffset2;
                    if (_self.workRow.customer_state != null && _self.workRow.customer_state != "") {
                        url += '&customer_state=' + _self.workRow.customer_state
                    }
                    if (_self.workRow.customer_level != null && _self.workRow.customer_level != "") {
                        url += '&customer_level=' + _self.workRow.customer_level
                    }
                    if (_self.workRow.customer_type != null && _self.workRow.customer_type != "") {
                        url += '&customer_type=' + _self.workRow.customer_type
                    }
                    if (_self.workRow.search_text != null && _self.workRow.search_text != "") {
                        url += '&search_text=' + _self.workRow.search_text
                    }
                    _self.$router.push({
                        path: url,
                        query: {
                            phone: row.phone
                        }
                    })
                },
                // 'click .edit_point':function (e, value, row, index) {
                //     _self.pointPara=row;
                //     $('#formPoint').parsley().reset();
                //     $('#editPointModal').modal('show');
                // }
            }

            $table.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('phone', '手机号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('name', '姓名'),
                    common.BTRowFormatWithFormatter('gender', '性别', genderInfo),
                    // common.BTRowFormatWithFormatter('age', '年龄',ageInfo),
                    // common.BTRowFormat('education', '学历'),
                    // common.BTRowFormat('career', '职业'),
                    // common.BTRowFormat('income', '收入'),
                    common.BTRowFormatWithFormatter('customer_level', '级别', clevelInfo),
                    common.BTRowFormatWithFormatter('', '地区', zoneFormatter),
                    common.BTRowFormatWithFormatterAlignLeft('decorate_address', '详细地址',common.remarkFormatter),
                    common.BTRowFormatWithFormatter('customer_source', '客户来源', customerSourceFormatter),
                    // common.BTRowFormatWithFormatterAlignLeft('flow_source', '流量来源', flowSourceFormatter),
                    common.BTRowFormatAlignLeft('salesperson_name', '销售'),
                    // common.BTRowFormatAlignRight('customer_point', '积分'),
                    /*common.BTRowFormatWithFormatter('customer_remarks', '备注',common.remarkFormatter),*/
                    common.BTRowFormatWithFormatter('customer_state', '状态', cstateInfo),
                    common.BTRowFormat('create_date', '创建日期'),
                    common.actFormatter('act', actFormatter, tableEvents),
                ],
                idField: 'user_id',
                uniqueId: 'user_id',
                toolbar: '#toolbar',
                clickToSelect: true,
                pagination: true,
                pageSize:common.pageSize(),
                pageNumber: nowPage,
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                striped: true,
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'user_id', $table)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    });
                    $('[data-name="customer_level"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        console.log('actrow',actrow);
                        if (actrow.user_id) {
                            $(this).editable({
                                type: 'Formatter',
                                emptytext: '无',
                                source: _self.pagePara['clevelInfo'],
                                disabled: true
                            })
                        }
                    })
                }
            })
            common.changeTableClass($table)
        }

        function initPage() {
            _self.$http.post(apiUrl + 'init', {}).then((response) => {
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                common.initSelect2($('#customer_state'), retData.cstateInfo)
                common.initSelect2($('#customer_level'), retData.clevelInfo)
                common.initSelect2($('#customer_type'), retData.ctypeInfo)
                common.initSelect2($('#gender'), retData.genderInfo)
                common.initSelect2($('#flow_source'), retData.flowSourceInfo)
                // common.initSelect2($('#age'), retData.ageInfo)
                domain_id = retData.userInfo.domain_id;

                //从其他页面返回，列表显示跳转前页码
                if (common.getUrlParams("offset") != null) {
                    tableOffset1 = parseInt(common.getUrlParams("offset"));
                }
                if (tableOffset1 != 0) {
                    nowPage = Math.floor(tableOffset1 / pageSize) + 1;
                } else {
                    nowPage = 1;
                }

                initTable()
                $('#formMod').parsley()
                common.reSizeCall()
                //                initSearch(retData)
                console.log('init success')
            }, (response) => {
                console.log('init error')
                common.dealErrorCommon(_self, response)
            })
        }

        $(function() {
            initPage()
            $('#distpicker').distpicker({
                placeholder: true
            });
        })

    },
    methods: {
        addM: function(event) {
            let _self = this
            _self.workRow = {}
            _self.modifyFlag = 0;
            $('#customer_state').val(null).trigger('change')
            $('#customer_level').val(null).trigger('change')
            // $('#customer_type').val(null).trigger('change')
            $('#gender').val(null).trigger('change')
            // $('#age').val(null).trigger('change')
            $('#flow_source').val(null).trigger('change')
            $('#distpicker').distpicker('reset', true);
            $('#modAct').hide()
            $('#addAct').show()
            $('#formMod').parsley().reset()
            $('#AddModal').modal('show')
        },
        submitMod: async function(event) {
            if ($('#formMod').parsley().isValid()) {
                let _self = this
                try {
                    _self.workRow.province = $('#province').val();
                    _self.workRow.city = $('#city').val();
                    _self.workRow.district = $('#district').val();
                    _self.workRow.flow_source = common.getSelect2Val('flow_source')
                    _self.workRow.gender = common.getSelect2Val('gender')
                    _self.workRow.customer_level = common.getSelect2Val('customer_level')
                    // _self.workRow.customer_type = common.getSelect2Val('customer_type')
                    _self.workRow.customer_state = common.getSelect2Val('customer_state')
                    // _self.workRow.age = common.getSelect2Val('age')
                    if (_self.modFlag === '1') {
                        let response = await _self.$http.post(apiUrl + 'add', _self.workRow)
                        let retData = response.data.info;
                        $('#table').bootstrapTable('insertRow', {
                            index: 0,
                            row: retData
                        });
                    } else if (_self.modFlag === '2') {
                        common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'user_id', $('#table'))
                    }
                    console.log('success')
                    $('#table').bootstrapTable("refresh", queryParams)
                    $('#AddModal').modal('hide')
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        search: function(event) {
            $('#table').bootstrapTable("refresh", queryParams)
        },
        customerleveleSwitch: function(value) {
            console.log('value',value)
            customer_level =  value
            $('#table').bootstrapTable("refresh", {query: {customer_level: value}})
        },
        // editPoint:async function () {
        //     let _self=this;
        //     try{
        //         if ($('#formPoint').parsley().isValid()) {
        //             if(_self.pointPara.customer_add_point<0){
        //                 if(Math.abs(_self.pointPara.customer_add_point)>_self.pointPara.customer_point){
        //                     return common.dealWarningCommon("积分减少值不能大于当前积分！");
        //                 }
        //             }
        //             let params=_self.pointPara;
        //             let response=await _self.$http.post(apiUrl + 'modify_point',params);
        //             let retData = response.data.info;
        //             if(response.data.errno==0){
        //                 $('#editPointModal').modal('hide');
        //                 _self.search();
        //             }
        //         }
        //     }catch (error){
        //         common.dealErrorCommon(_self, error);
        //     }
        //
        // }
    }
}
</script>
<style scoped>
.select-width {
    width: 180px;
}

.picker-width {
    width: 95px;
}

.address-label {
    position: absolute;
    top: 8px;
    left: 5px;
}

.address-input {
    width: 464px;
    margin-left: 53px;
}

.remark-label {
    position: absolute;
    top: 15px;
    left: 39px;
}

.remark-input {
    width: 464px;
    margin-left: 68px;
}

.flow-source-input {
    margin-left: 43px;
}
.font-style{
    font-family: 'Open Sans','Helvetica Neue',Helvetica,Arial,sans-serif;
    font-size: 12px;
    color: #242a30;
    font-weight: 600;
    display: inline-block;
    max-width: 100%;
}
.m_l{
    margin-left: -39px;
}
.m_r{
    margin-right: -70px;
}
.m_p{
    margin-left: 38px;
}
.p_l{
    padding-left: 0px;
    padding-right: 35px;
}
.margin_left{
    margin-left: -1px;
}
</style>
