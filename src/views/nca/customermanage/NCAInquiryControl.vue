<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">客户管理</a></li>
        <li class="active">询价列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">询价列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" id="search_text" placeholder="搜索姓名、手机号、地址" style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search()"><i class="fa fa-search"></i></button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="search(1)">
                            <a href="#tabletab" data-toggle="tab">
                            <span class="visible-xs">未受理</span>
                            <span class="hidden-xs">未受理</span>
                          </a>
                        </li>
                        <li class="" @click="search(2)">
                            <a href="#tabletab" data-toggle="tab">
                            <span class="visible-xs">已受理</span>
                            <span class="hidden-xs">已受理</span>
                          </a>
                        </li>
                        <li class="" @click="search(-1)">
                            <a href="#tabletab" data-toggle="tab">
                            <span class="visible-xs">全部</span>
                            <span class="hidden-xs">全部</span>
                          </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="tabletab">
                            <table id="table"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddInquiryModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增询价</h4>
                </div>
                <form @submit.prevent="addAct" id="formA">
                    <div class="modal-body">
                        <div class="form-group">
                            <label>手机号</label>
                            <input class="form-control" v-model="workRow.phone" data-parsley-required="true" data-parsley-phone="true">
                        </div>
                        <div class="form-group">
                            <label>套餐</label>
                            <select class="form-control select2" id="template_id" data-parsley-required="true"></select>
                        </div>
                        <div class="form-group">
                            <label>建筑面积</label>
                            <input class="form-control" v-model="workRow.house_area" data-parsley-required="true" data-parsley-maxlength="10">
                        </div>
                        <div class="form-group">
                            <label>城市</label>
                            <input class="form-control" v-model="workRow.area_code" maxlength="100" data-parsley-required="true" data-parsley-maxlength="100">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info"><i class="fa fa-fw fa-plus "></i>增加</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddCustomerModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">创建客户</h4>
                </div>
                <form @submit.prevent="modifyAct" id="formB">
                    <div class="modal-body">
                        <div class="form-group">
                            <label>客户姓名</label>
                            <input class="form-control" v-model="workRow.customer_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                        </div>
                        <div class="form-group">
                            <label>装修地址</label>
                            <input class="form-control" v-model="workRow.address" maxlength="100" data-parsley-maxlength="100">
                        </div>
                        <div class="form-group">
                            <label>户型</label>
                            <select class="form-control select2" id="pi_house_type"></select>
                        </div>
                        <div class="form-group">
                            <label>客户级别</label>
                            <select class="form-control select2" id="pi_customer_type"></select>
                        </div>
                        <div class="form-group">
                            <label>备注</label>
                            <input class="form-control" v-model="workRow.remark" maxlength="100" data-parsley-maxlength="100">
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info"><i class="fa fa-fw fa-plus "></i>确认</button>
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
                        <div class="form-group col-sm-12" >
                            <label class="col-sm-1 control-label">备注</label>
                            <div class="col-sm-11 left-padding">
                                <input class="form-control" v-model="workRow.remark" disabled>
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
const apiUrl = '/api/nca/customermanage/NCAInquiryControlControl?method='

export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            oldRow: '',
            inquiryState: 1
        }
    },
    name: 'NCAInquiryControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')

        function inquiryStateTypeFilter(value) {
            for (let i = 0; i < _self.pagePara.iStateInfo.length; i++) {
                if (_self.pagePara.iStateInfo[i].id === value) {
                    return _self.pagePara.iStateInfo[i].text
                }
            }
            return ''
        }

        function templateNameFilter(value) {
            for (let i = 0; i < _self.pagePara.templateInfo.length; i++) {
                if (_self.pagePara.templateInfo[i].id === value) {
                    return _self.pagePara.templateInfo[i].text
                }
            }
            return ''
        }

        function actFormatter(value, row) {
            if (row.inquiry_state === '1') {
                if (row.customer_exist == 1) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 create_customer">受理询价</a>',
                        '<a class="btn btn-info btn-xs m-r-5 edit_detail">查看</a>'
                    ].join('')
                } else {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 create_customer">创建客户</a>'
                    ].join('')
                }
            } else {
                return ''
            }
        }

        function acceptInquiry(id) {
            _self.workRow.inquiry_id = id
            _self.$http.post(apiUrl + 'accept', _self.workRow).then((response) => {
                $('#table').bootstrapTable("refresh", _self.queryParams)
                _self.workRow = {}
                common.dealSuccessCommon('询价受理成功')
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        }

        function initTable() {
            window.tableEvents = {
                'click .create_customer': function(e, value, row, index) {
                    if (row.customer_exist == 1) {
                        acceptInquiry(row.inquiry_id)
                    } else {
                        _self.oldRow = $.extend(true, {}, row);
                        _self.workRow = $.extend(true, {}, row);
                        $('#formB').parsley().reset()
                        $('#pi_customer_type').val(null).trigger('change');
                        $('#pi_house_type').val(null).trigger('change');
                        $('#AddCustomerModal').modal('show')
                    }
                },
                'click .show_detail': function(e, value, row, index) {
                    let url = '/crm/orderDetailControl?orderId=' + row.order_id;
                    _self.$router.push({
                        path: url
                    })
                },
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row);
                    _self.workRow = $.extend(true, {}, row);

                    $('#ModifyModal').modal('show')
                }
            }

            function queryParams(params) {
                if (_self.inquiryState > 0) {
                    params.inquiry_state = _self.inquiryState
                }
                if ($('#search_text').val()) {
                    params.search_text = $('#search_text').val()
                }
                return JSON.stringify(params)
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
                    common.BTRowFormatEditableLeft('customer_name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormat('area_code', '所在城市'),
//                    common.BTRowFormatWithFormatter('template_id', '套餐', templateNameFilter),
                    common.BTRowFormatAlignLeft('materiel_name', '套餐'),
                    common.BTRowFormatAlignRight('house_area', '房屋面积'),
                    common.BTRowFormatWithFormatterAlignRight('pre_offer', '快速报价',common.priceFormat),
                    common.BTRowFormatEdTextareaWidthAlignLeft('address', '装修地址',120,7),
                    common.BTRowFormatEdSelectAlignLeft(_self, 'house_type', '户型', 'hTypeInfo'),
                    // common.BTRowFormatEdTextareaWidth('remark', '备注',150,7),
                    common.BTRowFormatWithFormatter('inquiry_state', '状态', inquiryStateTypeFilter),
                    common.BTRowFormat('create_date', '提交日期'),
                    common.actFormatter('act', actFormatter, tableEvents)
                ],
                idField: 'inquiry_id',
                uniqueId: 'inquiry_id',
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                    $('[data-name="customer_name"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.inquiry_state != 2) {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true
                            })
                        }
                    })
                    $('[data-name="address"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.inquiry_state != 2) {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true
                            })
                        }
                    })
                    $('[data-name="house_type"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.inquiry_state != 2) {
                            $(this).editable({
                                type: 'select2',
                                emptytext: '无',
                                source: _self.pagePara['hTypeInfo'],
                                disabled: true
                            })
                        }
                    })
                    // $('[data-name="remark"]').each(function() {
                    //     let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                    //     if (actrow.inquiry_state != 2) {
                    //         $(this).editable({
                    //             type: 'text',
                    //             emptytext: '无',
                    //             display:function (value) {
                    //                 var html =  common.remarkFormatter(value);
                    //                 if(html) {
                    //                     $(this).html(html);
                    //                     $('[data-toggle="popover"]').each(function() {
                    //                         $(this).popover()
                    //                     });
                    //                 }
                    //             },
                    //             disabled: true
                    //         })
                    //     }
                    // })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'inquiry_id', $table)
                }

            })
            common.changeTableClass($table)
        }

        function initPage() {
            _self.$http.post(apiUrl + 'init', {}).then((response) => {
                let retData = response.data.info
                _self.pagePara = JSON.parse(JSON.stringify(retData))
                common.initSelect2($('#house_type'), retData.hTypeInfo)
                common.initSelect2($('#template_id'), retData.templateInfo)
                common.initSelect2($('#pi_customer_type'), retData.cTypeInfo)
                common.initSelect2($('#pi_house_type'), retData.hTypeInfo)
                initTable()
                $('#formA').parsley()
                $('#formB').parsley()
                common.reSizeCall()
                console.log('init success')
            }, (response) => {
                console.log('init error')
                common.dealErrorCommon(_self, response)
            })
        }

        $(function() {
            initPage()
        })
    },
    methods: {
        addM: function(event) {
            let _self = this
            _self.workRow = {}
            $('#formA').parsley().reset()
            $('#house_type').val(null).trigger('change')
            $('#template_id').val(null).trigger('change')
            $('#AddInquiryModal').modal('show')
        },
        addAct: function(event) {
            let _self = this
            if ($('#formA').parsley().isValid()) {
                _self.workRow.house_type = common.getSelect2Val('house_type')
                _self.workRow.template_id = common.getSelect2Val('template_id')
                _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                    $('#table').bootstrapTable("refresh", _self.queryParams)
                    _self.workRow = {}
                    common.dealSuccessCommon('增加成功')
                    console.log('add success')
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
        },
        modifyAct: function(event) {
            let _self = this
            _self.workRow.house_type = common.getSelect2Val('pi_house_type')
            _self.workRow.customer_type = common.getSelect2Val('pi_customer_type')
            if ($('#formB').parsley().isValid()) {
                _self.$http.post(apiUrl + 'modify', {
                    'old': _self.oldRow,
                    'new': _self.workRow
                }).then((response) => {
                    _self.workRow = {}
                    $('#table').bootstrapTable("refresh", _self.queryParams)
                    $('#AddCustomerModal').modal('hide')
                    common.dealSuccessCommon('用户创建成功')
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
        },
        search: function(index) {
            let _self = this
            if (index) {
                _self.inquiryState = index
            }
            $('#table').bootstrapTable("refresh", _self.queryParams)
        }
    }
}
</script>
<style scoped>
</style>
