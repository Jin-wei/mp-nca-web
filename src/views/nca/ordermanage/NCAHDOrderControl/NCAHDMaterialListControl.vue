<template>
<div class="panel panel-inverse">
    <div class="panel-body">
        <div class="col-md-12 no-padding">
            <div id="materielToolbar">
                <div class="form-inline" role="form">
                    <div class="form-group">
                        <div class="form-group">
                            <input class="form-control" multiple id="search_text" placeholder="搜索物料编码、物料名称、规格型号" style="width: 230px;">
                        </div>
                        <div class="form-group">
                            <select class="select2 form-control select-width" id="materiel_source_a">
                                <option value="">请选择采购来源</option>
                                <option v-for="source in pagePara.materielSourceInfo" v-bind:value="source.id">{{source.text}}</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <select class="select2 form-control select-width" id="materielProcedure">
                                <option value="">请选择工序</option>
                                <option v-for="b in pagePara.materielProcedure" v-bind:value="b.id">{{b.text}}</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <button id="search" class="btn btn-info" v-on:click="search">
                                <i class="fa fa-search"></i>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="col-md-12 no-padding">
            <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                <button type="button" class="btn btn-white" v-for="room in order.roomsInfo" v-bind:class="{active: selectRoomID == room.id}" @click="roomTypeSwitch(room)">{{room.text}}</button>
            </div>
            <table id="materielTable"></table>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const orderDetailApiUrl = '/api/nca/ordermanage/NCAHDOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            templateId: '',
            delivery_addr: '',
            searchedRow: [],
            mateRow: {},
            orderOD: {},
            materialList: [],
            selectRoomID: 1,
            selectRoomType: '',
            materiel_source: '2',
            fileUrl: '',
            filePath: '',
            processData: {}
        }
    },
    name: 'NCAHDMaterialListControl',
    mounted: function() {
        common.initDatepicker($('#pre_delivery_date'))
        $('#formdue').parsley()
    },
    watch: {
        pagePara: function() {

        },
        order: function() {
            let _self = this
            _self.templateId = _self.order.template_id
            if (_self.order.roomsInfo.length > 0) {
                _self.selectRoomID = _self.order.roomsInfo[0].id
            }

            let $table = $('#materielTable');
            let $processFile = $('#processFile');

            function queryParams(params) {
                if ($('#search_purchase_state').val()) {
                    params.purchase_state = $('#search_purchase_state').val();
                }
                if ($('#materiel_source_a').val()) {
                    params.materiel_source = $('#materiel_source_a').val();
                }
                if ($('#search_text').val()) {
                    params.search_text = $('#search_text').val();
                }
                if ($('#materielProcedure').val()) {
                    params.materiel_procedure = $('#materielProcedure').val()[0];
                }
                params.room_id = _self.selectRoomID
                params.order_id = common.getUrlParams("orderId");
                return JSON.stringify(params);
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: orderDetailApiUrl + 'MaterielCrmControlSRV_search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.materialList = res.info.rows;
                        return res.info;
                    },
                    columns: [
                      common.BTRowFormatWithIndex('NO.'),
                      common.BTRowFormat('materiel_code', '物料编码'),
                      common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                      common.BTRowFormat('materiel_format', '规格型号'),
                      common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                      common.BTRowFormatAlignRight('materiel_amount', '数量'),
                      common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价格',common.priceFormat),
                      common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价格',common.priceFormat),
                      common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                      common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
                      common.BTRowFormatEdSelect2Disabled(_self, 'materiel_amto', '类型', 'roomMaterialinfo'),
                      common.BTRowFormatEdSelect2Disabled(_self, 'room_type', '空间', 'roomTypeInfo')
                    ],
                    clickToSelect: true,
                    idField: 'ordermateriel_id',
                    uniqueId: 'ordermateriel_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        _self.$http.post(orderDetailApiUrl + 'MaterielCrmControlSRV_modify', {
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

            function initProcessFileTable() {
                $processFile.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('file_type', '文件类型'),
                        common.BTRowFormatAlignLeft('file_name', '文件名称'),
                        common.BTRowFormatWithFormatter('file_path', '文件链接', common.linkFormatter)
                    ],
                    idField: 'file_id',
                    uniqueId: 'file_id',
                    clickToSelect: true,
                    locale: 'zh-CN'
                })
                common.changeTableClass($processFile)
            }

            function initPage() {
                _self.workRow.materiel_remark = _self.order.materiel_remark
                _self.pagePara.roomsInfo = $.extend(true, [], _self.order.roomsInfo)
                common.initSelect2($('#materiel_state'), _self.pagePara.purchaseTypeInfo);
                initTable();
                initProcessFileTable();
            }

            $(function() {
                initPage()
            })
        }
    },
    methods: {
        checkState: function(state, event) {
            let _self = this;
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        search: function() {
            $('#materielTable').bootstrapTable("refresh")
        },
        roomTypeSwitch: function(room) {
            this.selectRoomID = room.id;
            this.selectRoomType = room.room_type;
            $('#materielTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
.select-width {
    width: 140px;
}
</style>
