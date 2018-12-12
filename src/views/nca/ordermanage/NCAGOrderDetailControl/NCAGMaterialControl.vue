<!--大客户物料清单-->
<template>
<div>
    <div>
        <div class="form-inline row">
            <div class="form-group">
                <div class="form-group">
                    <input class="form-control" id="search_text" placeholder="搜索物料编码、物料名称、规格型号" style="width: 230px;">
                </div>
                <div class="form-group">
                    <button id="search" class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                </div>
            </div>
        </div>
        <div class="panel panel-inverse" style="margin-top: 10px">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">物料列表</h4>
            </div>
            <div class="panel-body auto-height">
                <!--<ul class="nav nav-tabs">-->
                    <!--<li class="active"><a href="#default-tab" @click="procurementsources(2)" data-toggle="tab">集团采购</a></li>-->
                    <!--<li class=""><a href="#default-tab" @click="procurementsources(1)" data-toggle="tab">属地采购</a></li>-->
                <!--</ul>-->
                <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                    <button type="button" class="btn btn-white" v-for="room in order.roomsInfo" v-bind:class="{active: selectRoomId == room.id}" @click="roomTypeSwitch(room.id)">{{room.text}}</button>
                </div>
                <div class="tab-content">
                    <div class="tab-pane fade active in" id="default-tab">
                        <table id="materielTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAGOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            selectState: 2,
            selectRoomId:null
        }
    },
    name: 'NCAGMaterialControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {
            let _self = this;
        },
        order: function() {
            let _self = this;
            let $table = $('#materielTable');
            console.log(_self.order.roomsInfo);
            _self.selectRoomId = _self.order.roomsInfo[0].id;

            function queryParams(params) {
                params.order_id = _self.order.order_id;
                params.room_id = _self.selectRoomId
                params.materiel_source = _self.selectState.toString()
                if ($('#search_text').val()) {
                    params.search_text = $('#search_text').val();
                }
                return JSON.stringify(params);
            }

            function unitType(value, row) {
                for (let i = 0; i < _self.pagePara.unitInfo.length; i++) {
                    if (_self.pagePara.unitInfo[i].id == value) {
                        return _self.pagePara.unitInfo[i].text
                    }
                }
                return ''
            }

            function purchaseStateTypeFilter(value) {
                for (let i = 0; i < _self.pagePara.purchaseTypeInfo.length; i++) {
                    if (_self.pagePara.purchaseTypeInfo[i].id === value) {
                        return _self.pagePara.purchaseTypeInfo[i].text
                    }
                }
                return ''
            }

            function roomTypeFilter(value) {
                for (let i = 0; i < _self.order.roomsInfo.length; i++) {
                    if (_self.order.roomsInfo[i].room_id == value) {
                        return _self.order.roomsInfo[i].room_name
                    }
                }
                return ''
            }

            function initTable() {
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_material',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
//                        common.BTRowFormatWithIndex('NO.'),
//                        common.BTRowFormat('materiel_code', '物料编码'),
//                        common.BTRowFormat('materiel_name', '物料名称'),
//                        common.BTRowFormat('materiel_format', '规格型号'),
//                        common.BTRowFormatWithFormatter('room_id', '空间', roomTypeFilter),
//                        common.BTRowFormatWithFormatter('materiel_unit', '单位', unitType),
//                        common.BTRowFormat('materiel_amount', '数量'),
//                        common.BTRowFormat('materiel_batch', '批次'),
//                        common.BTRowFormatWithFormatter('purchase_state', '采购状态', purchaseStateTypeFilter),
//                        common.BTRowFormat('purchase_id', '采购单号'),
//                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('materiel_code', '物料编码'),
                        common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                        common.BTRowFormat('materiel_format', '规格型号'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                        common.BTRowFormatEditableOpt('materiel_amount', '数量', !_self.pagePara.thirdUser),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '分类', 'materialTypeInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '类别', 'materilaCategory'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_source', '采购来源', 'materielSourceInfo'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'materiel_procedure', '工序', 'materielProcedure'),
                        common.BTRowFormatWithFormatter('room_id', '空间', roomTypeFilter)
                    ],
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

            function initPage() {
                initTable();
            }
            $(function() {
                initPage()
            })
            $('#materielTable').bootstrapTable("refresh", queryParams)
        }
    },
    methods: {
        search: function() {
            let _self = this
            $('#materielTable').bootstrapTable("refresh", _self.queryParams)
        },
        procurementsources: function(value) {
            let _self = this
            _self.selectState = value
            $('#materielTable').bootstrapTable("refresh", _self.queryParams)
        },
        roomTypeSwitch: function(id) {
            let _self = this
            _self.selectRoomId = id
            $('#materielTable').bootstrapTable("refresh", _self.queryParams)
        }
    }
}
</script>
<style scoped>
</style>
