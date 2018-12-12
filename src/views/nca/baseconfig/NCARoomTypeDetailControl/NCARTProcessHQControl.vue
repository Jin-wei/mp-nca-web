<!--户型施工工序-->
<template>
<div>
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <button class="btn btn-info btn-xs" @click="addM">新建</button>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                    </div>
                    <h4 class="panel-title">施工工序</h4>
                </div>
                <div class="panel-body">
                    <table id="consNodeTable"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddConsNodeModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新建工序</h4>
                </div>
                <form @submit.prevent="addAct" id="formP">
                    <div class="modal-body row">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>工序名称</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple style="width:100%" id="gonode_name" data-parsley-required="true"></select>
                                </div>
                            </div>

                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>序号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.gonode_level" data-parsley-required="true" data-parsley-maxlength="10" data-parsley-type="integer">
                                </div>
                            </div>

                            <!--<div class="form-group col-sm-6">-->
                                <!--<label class="col-sm-4 control-label">开始日</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<input class="form-control" v-model="workRow.gostart_day" data-parsley-required="true" data-parsley-maxlength="10" data-parsley-type="integer" data-parsley-range="[1, 99999]">-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                        <div class="row">

                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>时长</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.gonode_duration" data-parsley-type="number" data-parsley-required="true">
                                </div>
                            </div>
                            <!--<div class="form-group col-sm-6">-->
                                <!--<label class="col-sm-4 control-label">天数</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<input class="form-control" v-model="workRow.gototal_day" @blur="listenDayChange" data-parsley-required="true" data-parsley-maxlength="10" data-parsley-type="integer" data-parsley-range="[1, 99999]">-->
                                <!--</div>-->
                            <!--</div>-->
                            <!--<div class="form-group col-sm-6">-->
                                <!--<label class="col-sm-4 control-label">完成日</label>-->
                                <!--<div class="col-sm-8">-->
                                    <!--<input class="form-control" v-model="goend_day" data-parsley-required="true" data-parsley-maxlength="10" data-parsley-type="integer" data-parsley-range="[1, 99999]">-->
                                <!--</div>-->
                            <!--</div>-->
                        </div>
                        <div class="row">
                            <div class="col-sm-12" style="padding-left: 6px">
                                <label class="col-sm-2 control-label"><span class="table-required">*</span>描述</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" v-model="workRow.gonode_description" maxlength="200" data-parsley-required="true" data-parsley-maxlength="200"></textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info" id="addAct">增加</button>
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
            gostartdays: [], //所有节点的开始日
            goindexes: [], //所有节点的序号
            goend_day: null
        }
    },
    name: 'RTProcessHQControl',
    watch: {
      pagePara: function() {
        let _self = this;
        common.initSelect2($('#gonode_name'), _self.pagePara.materielProcedure)
      },
        roomType: function() {
            let _self = this;
            let $consNodeTable = $('#consNodeTable');

            function queryParams(params) {
                params.roomtype_id = _self.roomType.roomtype_id;
                return JSON.stringify(params);
            }

            function actFormatter(value, row) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
                ].join('')
            }

            function initTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                        common.rowDeleteWithApi(_self, '空间删除', apiUrl + 'delete_p', $consNodeTable, row, 'gonode_id', function() {})
                    }
                };
                $consNodeTable.bootstrapTable({

                    method: 'POST',
                    url: apiUrl + 'search_p',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        for (let n of res.info.rows) {
                            _self.gostartdays.push(n.goend_day);
                            _self.goindexes.push(n.gonode_index);
                        }
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                       common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEdSelect2DisabledAlignLeft(_self, 'gonode_name', '工序', 'materielProcedure'),
//                        common.BTRowFormatEditable('gostart_day', '开始日'),
//                        common.BTRowFormatEditable('goend_day', '完成日'),
//                        common.BTRowFormatEditable('gonode_name', '工序名称'),
                        common.BTRowFormatEditable('gonode_level', '序号'),
                        common.BTRowFormatEditable('gonode_duration', '时长'),
                        common.BTRowFormatEditablePop('gonode_description', '描述'),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ],
                    idField: 'roomtype_id',
                    uniqueId: 'roomtype_id',
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_p', row, 'gonode_id', $consNodeTable)
                    }
                });
                common.changeTableClass($consNodeTable)
            }

            initTable();
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            _self.goend_day = null;
            $('#gonode_name').val(null).trigger('change');
            $('#formP').parsley().reset();
            _self.workRow.gostart_day = _self.gostartdays.length > 0 ? Math.max.apply(null, _self.gostartdays) + 1 : 1;
            _self.workRow.gonode_index = _self.goindexes.length > 0 ? Math.max.apply(null, _self.goindexes) + 1 : 1;
            $('#AddConsNodeModal').modal('show');
        },
        listenDayChange: function() {
            let _self = this;
            if (parseInt(_self.workRow.gototal_day) > 0 && parseInt(_self.workRow.gostart_day) > 0) {
                _self.goend_day = parseInt(_self.workRow.gostart_day) + parseInt(_self.workRow.gototal_day) - 1
            }
        },
        addAct: async function(event) {
            let _self = this;
            let formP = $('#formP');
            if (formP.parsley().isValid()) {
                _self.workRow.roomtype_id = _self.roomType.roomtype_id;
//                _self.workRow.goend_day = _self.goend_day;
                _self.workRow.gonode_name = common.getSelect2Val('gonode_name')
                try {
                    let response = await _self.$http.post(apiUrl + 'add_p', _self.workRow);
                    let retData = response.data.info;
                    _self.$parent.updateRoomType()
                    $('#consNodeTable').bootstrapTable("refresh");
                    formP.parsley().reset();
                    $('#AddConsNodeModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        }

    }
}
</script>
<style scoped>
</style>
