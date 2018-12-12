<!--户型空间列表-->
<template>
<div>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button class="btn btn-info btn-xs" v-on:click="addRoomM">增加</button>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">空间列表</h4>
            </div>
            <div class="panel-body">
                <table id="orderRoomTable"></table>
            </div>
        </div>
    </div>
    <div class="modal fade" id="addOrderRoomModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">增加空间</h4>
                </div>
                <form @submit.prevent="addRoomAct" id="formA">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>空间类型</label>
                                <div class="col-md-9">
                                    <select class="form-control select2" id="room_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>空间名称</label>
                                <div class="col-md-9">
                                    <input class="form-control" v-model="workRow.goroom_name" maxlength="25" data-parsley-maxlength="25" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">增加</button>
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

async function getTablesData(_self) {
    try {
        let $orderRoomTable = $('#orderRoomTable');
        let param = {
            roomtype_id: common.getUrlParams("roomTypeId")
        }
        let response = await _self.$http.post(apiUrl + 'search_room', param);
        let retdata = response.data.info

        $orderRoomTable.bootstrapTable('load', {
            data: retdata
        });
    } catch (error) {
        common.dealErrorCommon(_self, error);
    }
}

export default {
    props: ['roomType', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: ''
        }
    },
    name: 'RTSpaceHQControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#room_type'), _self.pagePara.roomTypeInfo);
        },
        roomType: function() {
            let _self = this;
            let $orderRoomTable = $('#orderRoomTable');

            function roomTypeFormatter(value, row) {
                for (let i = 0; i < _self.pagePara.roomTypeInfo.length; i++) {
                    if (_self.pagePara.roomTypeInfo[i].id === value) {
                        return _self.pagePara.roomTypeInfo[i].text
                    }
                }
                return ''
            }

            window.tableEvents = {
                'click .order_room_delete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '空间删除', apiUrl + 'delete_room', $orderRoomTable, row, 'goroom_id', function() {
                        getTablesData(_self)
                    })
                }
            }

            function roomDeletFormatter(value, row) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 order_room_delete btn-info-delete">删除</a>',
                ].join('')
            }


            function initOrderRoomTable() {
                $orderRoomTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFormatter('goroom_type', '空间类型', roomTypeFormatter),
                        common.BTRowFormatAlignLeft('goroom_name', '空间名称'),
                        common.actFormatter('act', roomDeletFormatter, tableEvents)
                    ],
                    idField: 'goroom_id',
                    uniqueId: 'goroom_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    striped: true
                });

                $('#formA').parsley()
                common.changeTableClass($orderRoomTable)
            }

            initOrderRoomTable()
            getTablesData(_self)

        }
    },
    methods: {
        addRoomM: function(event) {
            let _self = this
            _self.workRow = {}
            $('#room_type').val(null).trigger('change')
            $('#formA').parsley().reset()
            $('#addOrderRoomModal').modal('show')
        },
        addRoomAct: async function(event) {
            let _self = this
            if ($('#formA').parsley().isValid()) {
                try {
                    _self.workRow.roomtype_id = _self.roomType.roomtype_id
                    _self.workRow.goroom_type = common.getSelect2Val('room_type')
                    let response = await _self.$http.post(apiUrl + 'add_room', _self.workRow);
                    let retData = response.data.info;
                    _self.$parent.updateRoomType()
                    $('#addOrderRoomModal').modal('hide')
                    getTablesData(_self)
                } catch (error) {
                    console.log('init error');
                    common.dealErrorCommon(_self, error);
                }
            }
        },
    }
}
</script>
<style scoped>
</style>
