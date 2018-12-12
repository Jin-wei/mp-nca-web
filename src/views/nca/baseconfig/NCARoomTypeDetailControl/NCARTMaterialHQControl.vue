<!--户型物料清单-->
<template>
<div>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button v-show="disabled" class="btn btn-info btn-xs" @click="addM">增加</button>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">物料列表</h4>
            </div>
            <div class="panel-body">
                <div class="form-inline">
                    <div class="form-group ">
                        <select class="select2 form-control" id="material_category" style="width: 150px" v-model="selectCategory">
                                <option value="">请选择类型</option>
                                <option v-for="category in pagePara.materilaCategory" v-bind:value="category.id">{{category.text}}</option>
                            </select>
                    </div>
                    <div class="form-group ">
                        <button class="btn btn-info" v-on:click="search">
                                <i class="fa fa-search"></i>
                            </button>
                    </div>
                </div>
                <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                    <button type="button" class="btn btn-white" v-for="room in roomType.roomsInfo" v-bind:class="{active: selectRoomId == room.id}" @click="roomTypeSwitch(room.id)">{{room.text}}</button>
                </div>
                <table id="materielTable"></table>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width:1100px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增物料</h4>
                </div>
                <form @submit.prevent="addAct" id="mForm">
                    <div class="modal-body">
                        <div>
                            <div class="form-group col-xs-11" style="padding-left: 0px">
                                <input class="form-control" v-model="mateRow.matNameOrCodeOrFormat" id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                            </div>
                            <!--<div class="form-group col-xs-4">-->
                                <!--<label class="col-sm-4 control-label"><i class="symbol">*</i>物料类型</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<select class="form-control select2" multiple style="width:100%" id="category_type" data-parsley-required="true"></select>-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group col-xs-4">-->
                                <!--<label class="col-sm-4 control-label"><i class="symbol" style="padding-left: 10px">*</i>空间</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<select class="form-control select2" multiple style="width:100%" id="room_space" data-parsley-required="true"></select>-->
                                <!--</div>-->
                            <!--</div>-->
                            <div class="form-group col-xs-1" style="padding-left: 35px;padding-right: 0px">
                                <button type="submit" class="btn btn-info" id="addAct">增加</button>
                            </div>
                        </div>
                        <div>
                            <!--<div class="btn-group">-->
                                <!--<button type="button" class="btn btn-white" @click="materielsourceSwitch('2')" data-toggle="tab">集团采购</button>-->
                                <!--<button type="button" class="btn btn-white" @click="materielsourceSwitch('1')" data-toggle="tab">属地采购</button>-->
                            <!--</div>-->
                            <table id="tableSearch"></table>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCARoomTypeDetailControl?method=';

export default {
    props: ['roomType', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            searchedRow: [],
            mateRow: {},
            materialList: [],
            selectCategory: '',
            selectRoomId: null,
            materiel_source:'2',
            selectSource: null, //物料来源Tab
            disabled: ''
        }
    },
    name: 'materialHQControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {
            let _self = this;
            _self.selectSource = _self.pagePara.materielSourceInfo[0].id
            //common.initSelect2($('#category_type'), _self.pagePara.materilaCategory)
        },
        roomType: function() {
            let _self = this;
            let $table = $('#materielTable');
            let $tableSearch = $('#tableSearch');
            _self.disabled = _self.roomType.roomsInfo.length>0 ? true : false
            if(_self.roomType.roomsInfo && _self.roomType.roomsInfo.length>0){
                _self.selectRoomId = _self.roomType.roomsInfo[0].id
            }else {
                common.dealPromptCommon('请先增加空间');
                return
            }
            function deleteFormatter(value, row, index) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 delete btn-info-delete">删除</a>'
                ].join('')
            }

            function queryParams(params) {
                params.roomtype_id = common.getUrlParams("roomTypeId")
                params.gomateriel_type = _self.selectCategory
                params.goroom_id = _self.selectRoomId;
                return JSON.stringify(params);
            }

            function queryParamsMateriel(params) {
                params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
//                params.materiel_state = 1
//                params.materiel_source = _self.materiel_source;
                return JSON.stringify(params)
            }

            window.tableEvents = {
                'click .delete': function(e, value, row, index) {
                    rowDelete(_self, '物料删除', apiUrl, row, 'goordermateriel_id')
                },
                'click .searchRow': function(e, value, row, index) {
                    if ($(this).attr("checked") == 'checked') {
                        _self.searchedRow.push(row)
                    } else {
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    }
                }
            };


            let rowDelete = function(_self, msg, apiUrl, row, key) {
                common.dealConfrimCommon(msg, function() {
                    _self.$http.post(apiUrl + 'delete_material', row).then((response) => {
                        $('#materielTable').bootstrapTable("refresh", queryParams)
                        common.dealSuccessCommon('删除成功');
                    }, (response) => {
                        common.dealErrorCommon(_self, response);
                    });
                });
            };

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_material',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.materialList = res.info.rows;
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEditableRight('gomateriel_amount', '数量'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'gomateriel_type', '类别', 'materilaCategory'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
//                        common.BTRowFormat('gomateriel_batch', '批次'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_procedure', '工序', 'materielProcedure'),
                        common.actFormatter('deleteAct', deleteFormatter, tableEvents)
                    ],
                    idField: 'goordermateriel_id',
                    uniqueId: 'goordermateriel_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        _self.$http.post(apiUrl + 'modify_material', {
                            'old': _self.oldRow,
                            'new': row
                        }).then((response) => {
                            $('#materielTable').bootstrapTable("refresh", queryParams)
                        }, (response) => {
                            common.dealErrorCommon(this, response)
                        })
                    }
                })
                common.changeTableClass($table)
            }

            function initTableMateriel() {
                $tableSearch.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_mat',
                    queryParams: queryParamsMateriel,
                    sidePagination: 'server',
                    ajaxOptions: function() {
                        let headers = {};
                        headers.authorization = common.getStoreData('token');
                        return {
                            headers: headers
                        }
                    },
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [{
                            field: 'state',
                            checkbox: true
                        },
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
//                        common.BTRowFormatEdSelect2(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_procedure', '工序', 'materielProcedure'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '类型', 'roomMaterialinfo'),
                        common.BTRowFormat('domain_name', '所属机构')
                    ],
                    idField: 'materiel_id',
                    uniqueId: 'materiel_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {},
                    onEditableSave: function(field, row, oldValue, $el) {},
                    onCheckAll: function(rows) {   //全选
                        for (let i = 0; i < rows.length; i++) {
                            _self.searchedRow.push(rows[i])
                        }
                    },
                    onUncheckAll: function(rows) {   //取消全选
                        _self.searchedRow = []
                    },
                    onCheck: function(row, $element) {   //单个选择
                        _self.searchedRow.push(row)
                    },
                    onUncheck: function(row, $element) {   //单个取消
                        _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                    },
                    onPageChange: function(number, size) {
                        _self.searchedRow = []
                    }
                })
                common.changeTableClass($tableSearch)
            }

            function initPage() {
                common.initSelect2($('#room_space'), _self.roomType.roomsInfo)
                initTable();
                initTableMateriel();
                $('#mForm').parsley();
            }

            $(function() {
                initPage()
                $('#materielTable').bootstrapTable("refresh", queryParams)
            })
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            //$('#category_type').val(null).trigger('change');
            $('#room_space').val(null).trigger('change')
            $('#AddMaterielModal').modal('show')
            $('#mForm').parsley().reset();
            _self.searchedRow = []
            $("input[type=checkbox]").each(function() {
                $(this).prop("checked", false);
            })
        },
        addAct: function() {
            let _self = this;
            if ($('#mForm').parsley().isValid()) {
                for (let i = 0; i < _self.searchedRow.length; i++) {
                    _self.workRow = {}
                    _self.workRow.roomtype_id = common.getUrlParams("roomTypeId")
                    _self.workRow.gomateriel_id = _self.searchedRow[i].materiel_id
                    _self.workRow.gomateriel_batch = _self.searchedRow[i].materiel_batch
                    _self.workRow.gomateriel_type = _self.searchedRow[i].materiel_amto
                    //_self.workRow.gomateriel_type = common.getSelect2Val('category_type')
                    _self.workRow.goroom_id = _self.selectRoomId
                    _self.$http.post(apiUrl + 'add_material', _self.workRow).then((response) => {
                        $('#materielTable').bootstrapTable("refresh", _self.queryParams)
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
                $('#AddMaterielModal').modal('hide')
            }
        },
        searchMate: function(event) {
            let _self = this;
            _self.searchedRow = [];
            $('#tableSearch').bootstrapTable("refresh", _self.queryParamsMateriel)
        },
        roomTypeSwitch: function(id) {
            let _self = this
            _self.selectRoomId = id
            $('#materielTable').bootstrapTable("refresh", _self.queryParams)
        },
        search: function() {
            let _self = this;
            $('#materielTable').bootstrapTable("refresh", _self.queryParams)
        },
        materielsourceSwitch: function(value) {
            let _self = this
            _self.materiel_source = value
            $('#tableSearch').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
.symbol {
    color: red;
    font-size: 20px;
    position: absolute;
    top: 6px;
    left: -2px;
}
</style>
