<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">CRM</a></li>
        <li class="active">订单管理</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">订单管理</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input class="form-control" v-model="workRow.search_text" id="search_text" placeholder="搜索订单号、姓名、手机号、地址" style="width: 230px;">
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="search_order_state">
                                        <option value="">请选择状态</option>
                                        <option v-for="state in pagePara.orderStateInfo" v-bind:value="state.id">{{state.text}}</option>
                                    </select>
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="search_order_type">
                                        <option value="">请选择工程类型</option>
                                        <option v-for="type in pagePara.orderTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" id="start_date" placeholder="开始时间" />
                            </div>
                            <div class="form-group">
                                <span>-</span>
                                <input type="text" class="form-control" id="end_date" placeholder="结束时间" />
                            </div>
                            <div class="form-group ">
                                <button id="search" class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                            </div>
                        </div>
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM">
                                        <i class="glyphicon glyphicon-plus"></i>
                                        新增订单
                                    </button>
                        </div>
                    </div>
                </div>

                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <ul class="nav nav-tabs">
                        <li class="active"><a href="#default-tab" @click="search(0)" data-toggle="tab">装修订单</a></li>
                        <li class=""><a href="#default-tab" @click="search(7)" data-toggle="tab">团体订单</a></li>
                        <li class=""><a href="#default-tab" @click="search(8)" data-toggle="tab">销售订单</a></li>
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
    <div class="modal fade" id="AddModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 v-if="workRow.order_id" class="modal-title">编辑订单</h4>
                    <h4 v-else class="modal-title">新建订单</h4>
                </div>
                <form @submit.prevent="saveAct" id="formA">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">客户手机</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.phone" @blur="searchbyphone" data-parsley-phone="true" data-parsley-required="true" data-parsley-group="block-1" :disabled="act===2">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">客户姓名</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.name" :disabled="act===2" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50" data-parsley-group="block-1">
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">工程类型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple style="width:100%" id="order_type" data-parsley-required="true" data-parsley-group="block-1"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">推荐人手机</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="recommender_phone" v-model="workRow.recommender_phone" data-parsley-phone="true" data-parsley-group="block-1" :disabled="act===2">
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div data-toggle="distpicker" id="distpicker">
                                <div class="col-sm-4">
                                    <span class="col-sm-4 control-label font-style">省份</span>
                                    <div class="col-sm-8">
                                        <select class="form-control picker-width" id="province" v-model="workRow.province"></select>
                                    </div>
                                </div>
                                <div class="col-sm-4">
                                    <span class="col-sm-5 control-label font-style">市/县</span>
                                    <div class="col-sm-5">
                                        <select class="form-control picker-width" id="city" v-model="workRow.city"></select>
                                    </div>
                                </div>
                                <div class="col-sm-4">
                                    <span class="col-sm-4 control-label font-style">区</span>
                                    <div class="col-sm-8">
                                        <select class="form-control picker-width" id="district" v-model="workRow.district" @change="getEstate"></select>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div class="row" style="margin-top: 15px;">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">楼盘</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="estate" data-parsley-required="true" data-parsley-group="block-1"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">楼栋</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="build" data-parsley-required="true" data-parsley-group="block-1"></select>
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">单元</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="unit" data-parsley-required="true" data-parsley-group="block-1"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">房间号</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="room_no" data-parsley-required="true" data-parsley-group="block-1"></select>
                                </div>
                            </div>
                        </div>

                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">户型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="order_house_type"  data-parsley-group="block-1"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">建筑面积</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="order_house_area" data-parsley-group="block-1" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12" v-show="workRow.order_id">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">状态</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="order_state" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">开工日期</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.break_date" id="break_date" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">设计师</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="order_designer" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">监理</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple style="width:100%" id="order_supervision" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label">订单描述</label>
                                    <div class="col-sm-10">
                                        <input class="form-control" v-model="workRow.order_remark">
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" @click="act=1" id="addAct">提交
                            </button>
                        <button type="submit" class="btn btn-info" @click="act=2" id="modAct">修改
                            </button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <div class="modal fade" id="QRModal" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">二维码</h4>
                </div>
                <div class="modal-body" v-html="QRHtml">

                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAOrderControl?method=';



export default {
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            oldRow: '',
            act: 0,
            QRHtml:'',
            orderType:''
        }
    },
    name: 'NCAOrderControl',
    mounted: function() {
        let _self = this
        let $table = $('#table')

        function actFormatter(value, row) {
            return [
                '<a class="edit_detail">',
                '<i class="fa fa-pencil"></i>',
                '</a>'
            ].join('')
        }

        function nameFormatter(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer;">',
                value,
                '</a>'
            ].join('')
        }

        function operateFormatter(value, row, index) {
            return [
                '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">查看二维码</a>',
            ].join('')
        }

        function initSearch(retData) {
            $('#search_order_state').select2({
                placeholder: '请选择状态',
                allowClear: true,
                maximumSelectionLength: 1,
                language: 'zh-CN',
                tags: false,
                width: 150,
                data: retData.orderStateInfo
            })

            $('#search_order_type').select2({
                placeholder: '请选择工程类型',
                allowClear: true,
                maximumSelectionLength: 1,
                language: 'zh-CN',
                tags: false,
                width: 150,
                data: retData.orderTypeInfo
            })
        }
        function queryParams(params) {
            if ($('#search_order_state').val()) {
                params.order_state = $('#search_order_state').val()
            }

            if(_self.orderType!=7 && _self.orderType !=8){
                if ($('#search_order_type').val()) {
                    params.order_type = $('#search_order_type').val()
                }
            }else {
                params.order_type = _self.orderType
            }

            if ($('#start_date').val()) {
                params.created_at_start = $('#start_date').val()
            }
            if ($('#end_date').val()) {
                params.created_at_end = $('#end_date').val()
            }
            if ($('#search_text').val()) {
                params.search_text = $('#search_text').val()
            }
            params.is_crm = '1';
            return JSON.stringify(params)
        }
        function initTable() {
            window.tableEvents = {
                'click .edit_detail': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row);
                    _self.workRow = $.extend(true, {}, row);
                    $('#order_state').val(_self.workRow.order_state).trigger('change');
                    $('#order_type').val(_self.workRow.order_type).trigger('change');
                    //$('#order_house_type').val(_self.workRow.order_house_type).trigger('change');
                    //$('#order_house_area').val(_self.workRow.order_house_area).trigger('change');
                    $('#modAct').show();
                    $('#addAct').hide();
                    _self.act = 2;
                    $('#AddModal').modal('show')
                },
                'click .show_detail': function(e, value, row, index) {
                    if(row.order_type === '7') {
                      _self.$router.push({
                          path: '/nca/ordermanage/NCAGOrderDetailControl',
                          query: {
                              orderId: row.order_id
                          }
                      })
                    } else {
                      _self.$router.push({
                          path: '/nca/ordermanage/NCAOrderDetailControl',
                          query: {
                              orderId: row.order_id
                          }
                      })
                    }
                },
                'click .tableDelete': function(e, value, row, index) {
                    _self.$http.post(apiUrl + 'qrcode', {order_id: row.order_id}).then((response) => {
                        $('#QRModal').modal('show');
                        _self.QRHtml = response.data.info.qrcode;
                    }, (response) => {
                    });
                }
            };

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
                    common.BTRowFormatWithFE('order_id', '订单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('name', '客户姓名'),
                    common.BTRowFormat('phone', '客户手机'),
                    common.BTRowFormatEditableLeft('order_address', '装修地址',common.remarkFormatter),
                    common.BTRowFormatEditableRight('order_house_area', '面积（平米）'),
                    common.BTRowFormatEdSelect2(_self, 'order_type', '工程类型', 'orderTypeInfo'),
                    common.BTRowFormatEdSelect2(_self, 'order_state', '状态', 'orderStateInfo'),
                    common.BTRowFormat('designer_name', '设计师'),
                    common.BTRowFormat('create_date', '创建日期'),
                    common.BTRowFormat('recommender_phone', '推荐人手机'),
                    common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    //common.actFormatter('act', actFormatter, tableEvents)
                ],
                idField: 'order_id',
                uniqueId: 'order_id',
                toolbar: '#toolbar',
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-name="order_address"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.order_state != 'NEW' && actrow.order_state != 'DESIGNING') {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true
                            })
                        }
                    })
                    $('[data-name="order_house_type"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.order_state != 'NEW' && actrow.order_state != 'DESIGNING') {
                            $(this).editable({
                                type: 'select2',
                                emptytext: '无',
                                source: _self.pagePara['hTypeInfo'],
                                disabled: true
                            })
                        }
                    })
                    $('[data-name="order_house_area"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.order_state != 'NEW' && actrow.order_state != 'DESIGNING') {
                            $(this).editable({
                                type: 'text',
                                emptytext: '无',
                                disabled: true
                            })
                        }
                    })
                    $('[data-name="order_type"]').each(function() {
                        let actrow = $('#table').bootstrapTable('getRowByUniqueId', this.getAttribute('data-pk'))
                        if (actrow.order_state != 'NEW' && actrow.order_state != 'DESIGNING') {
                            $(this).editable({
                                type: 'select2',
                                emptytext: '无',
                                source: _self.pagePara['orderTypeInfo'],
                                disabled: true
                            })
                        }
                    })
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'order_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initPage() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2($('#order_type'), retData.orderTypeInfo);
                common.initSelect2($('#order_state'), retData.orderStateInfo);
                common.initSelect2($('#order_designer'), retData.userListInfo);
                common.initSelect2($('#order_supervision'), retData.userListInfo);
//                common.initSelect2($('#order_house_type'), retData.hTypeInfo);
                common.initSelect2Editable($('#estate'), []);
                common.initSelect2Editable($('#build'), []);
                common.initSelect2Editable($('#unit'), []);
                common.initSelect2Editable($('#room_no'), []);
                common.initSelect2Editable($('#order_house_type'), []);


                //                    initSearch(retData);
                initTable();
                $('#formA').parsley();
                common.reSizeCall();
                console.log('init success')
            } catch (error) {
                console.log('init error');
                common.dealErrorCommon(_self, error);
            }
            common.initDatepicker($('#start_date'));
            common.initDatepicker($('#end_date'));
            common.initDatepicker($('#break_date'));

        }

      //从客户管理中创建的快速下单
      function quickAdd () {
        let phone = _self.$route.query.phone
        let name = _self.$route.query.name
        let province = _self.$route.query.province
        let city = _self.$route.query.city
        let district = _self.$route.query.district
        let address = _self.$route.query.address

        if (phone && name){
          _self.workRow = {}
          _self.workRow.phone = phone
          _self.workRow.name = name

          _self.workRow.order_address = province + city + district + address
          $('#order_type').val(null).trigger('change');
          $('#order_house_type').val(null).trigger('change');
          $('#modAct').hide();
          $('#addAct').show();
          _self.act = 1;
          $('#AddModal').modal('show');
        }
      }

        $('#estate').on('select2:select', async function(evt) {
            let params={
                estate_id:common.getSelect2Val('estate')
            }
            let response = await _self.$http.post(apiUrl + 'getBuild', params);
            let retData = response.data.info;
            $("#build").html('');
            common.initSelect2Editable($('#build'), retData);
        });
        $('#build').on('select2:select', async function(evt) {
            let params={
                estate_id:common.getSelect2Val('estate'),
                build:common.getSelect2Val('build')
            }
            let response = await _self.$http.post(apiUrl + 'getUnit', params);
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
            let response = await _self.$http.post(apiUrl + 'getRoomNo', params);
            let retData = response.data.info;
            $("#room_no").html('');
            common.initSelect2Editable($('#room_no'), retData);
        });
        $('#room_no').on('select2:select', async function(evt) {
            let params={
                room_id:common.getSelect2Val('room_no')
            }
            let response = await _self.$http.post(apiUrl + 'getRoomType', params);
            let retData = response.data.info;
            $("#order_house_type").html('');
            common.initSelect2($('#order_house_type'), retData);
        });
//        $('#order_house_type').on('select2:select', async function(evt) {
//            let params={
//                estate_id:common.getSelect2Val('estate'),
//                //name:common.getSelect2Val('order_house_type'),
//            }
//            let response = await _self.$http.post(apiUrl + 'getArea', params);
//            let retData = response.data.info;
//            $("#order_house_area").html('');
//            common.initSelect2($('#order_house_area'), retData);
//            console.log('$("#order_house_area")'+$('#order_house_area').val())
//        });
        $(function() {
            initPage()
            quickAdd()
            $('#distpicker').distpicker({
                placeholder: true
            });
        });
    },
    methods: {
        saveAct: async function(event) {
            let _self = this;
            let formA = $('#formA');
            if (_self.act === 1) {
                if (formA.parsley().isValid({
                        group: 'block-1'
                    })) {
                    try {
                        _self.workRow.order_type = common.getSelect2Val('order_type');
                        _self.workRow.room_id=common.getSelect2Val('room_no');
                        _self.workRow.order_address=
                            $('#province').val()+
                            $('#city').val()+
                            $('#district').val()+'-'+
                            $('#estate option:selected').text()+
                            $('#build option:selected').text() + '栋' +
                            $('#unit option:selected').text() + '单元' +
                            $('#room_no option:selected').text()+ '号';

                        _self.workRow.province= $('#province').val();
                        _self.workRow.city= $('#city').val();
                        _self.workRow.district=$('#district').val()
                        _self.workRow.estate_name=$('#estate option:selected').text()
                        _self.workRow.build=$('#build option:selected').text()
                        _self.workRow.unit=$('#unit option:selected').text()
                        _self.workRow.room_no=$('#room_no option:selected').text()
                        _self.workRow.order_house_type=$('#order_house_type option:selected').text()
                        _self.workRow.order_house_area=$('#order_house_area').val()
                        let roomtype_id=common.getSelect2Val('order_house_type');
                        _self.workRow.roomtype_id=(roomtype_id=='')?0:roomtype_id
                        _self.workRow.recommender_phone=$('#recommender_phone').val()

                        let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                        $('#table').bootstrapTable("refresh", queryParams)
                        _self.workRow = {};
                        console.log('add success');
                        formA.parsley().reset();
                        $('#AddModal').modal('hide');
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            } else if (_self.act === 2) {
                if (formA.parsley().isValid()) {
                    _self.workRow.break_date = $("#break_date").val();
                    _self.workRow.order_supervision = common.getSelect2Val('order_supervision');
                    _self.workRow.order_designer = common.getSelect2Val('order_designer');
                    common.rowModifyWithT(_self, apiUrl + 'modify', _self.workRow, 'order_id', $('#table'));
                    formA.parsley().reset();
                    $('#AddModal').modal('hide');
                }
            }
        },
        addM: function(event) {
            let _self = this;

            _self.workRow = {};
            $('#order_type').val(null).trigger('change');
            $('#order_house_type').val(null).trigger('change');

            $("#estate").val(null).trigger('change');
            $("#build").val(null).trigger('change');
            $("#unit").val(null).trigger('change');
            $("#room_no").val(null).trigger('change');

            $('#modAct').hide();
            $('#addAct').show();
            _self.act = 1;

            $('#distpicker').distpicker('reset', true);
            $('#distpicker').distpicker('destroy', true);
            $('#distpicker').distpicker({
                province: "",
                city: "",
                district: ""
            });
            $('#AddModal').modal('show');
        },
        search: function(params) {
            let _self = this
            _self.orderType=params;
            console.log(_self.orderType)
            $('#table').bootstrapTable("refresh")
        },
        searchbyphone: function(event) {
            let _self = this;
            async function getUser() {
                try {
                    let response = await _self.$http.post(apiUrl + 'searchPhone', _self.workRow);
                    let retData = response.data.info;
                    if (!$.isEmptyObject(retData)) {
                        _self.$set(_self.workRow, 'name', retData.name)
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            if (_self.workRow.phone) {
                getUser()
            }
        },
        addImg: function(event) {
            $('#AddModalImg').modal('show')
        },
        getEstate:async function(event){
            try {
                $('#estate').html('');
                let _self = this;
                let params= {
                    province:_self.workRow.province,
                    city:_self.workRow.city,
                    district:_self.workRow.district
                };
                let response = await _self.$http.post(apiUrl + 'getEstate', params);
                let retData = response.data.info;
                common.initSelect2Editable($('#estate'), retData);

            } catch (error) {
                common.dealErrorCommon(_self, error)
            }

        }
    }
}
</script>
<style scoped>
.select-width {
    width: 150px;
}
.picker-width {
    width: 81px;
}
.font-style{
    font-family: 'Open Sans','Helvetica Neue',Helvetica,Arial,sans-serif;
    font-size: 12px;
    color: #242a30;
    font-weight: 600;
    display: inline-block;
    max-width: 100%;
}
</style>
