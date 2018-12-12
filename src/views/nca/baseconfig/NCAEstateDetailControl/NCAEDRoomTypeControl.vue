<template>
<div class="panel-body" style="padding-top: 0px">
    <div>
        <div id="roomTypeToolbar" v-show="!pagePara.thirdUser" style="padding-bottom: 0px">
            <div class="form-inline" role="form">
                <!--<div class="form-inline">-->
                    <div class="form-group pull-right" style="padding-top:0px;padding-bottom: 15px">
                        <button class="btn btn-info" v-on:click="addM">增加
                        </button>
                        <button class="btn btn-info" v-on:click="syncAct">
                            同步
                        </button>

                    </div>
                <!--</div>-->
            </div>
        </div>
        <table id="roomTypeTable"></table>
    </div>

    <div class="modal fade" id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增户型</h4>
                </div>
                <div class="modal-body">
                    <div class="row">
                        <div class="form-group col-xs-6">
                            <label class="">户型名称:</label>
                            <input class="form-control" v-model="workRow.roomtype_name" maxlength="20">
                        </div>
                        <div class="form-group col-xs-6">
                            <label>户型的房型:</label>
                            <input class="form-control" v-model="workRow.roomtype_room_count" maxlength="20">
                        </div>
                    </div>
                    <div class="row">
                        <div class="form-group col-xs-6">
                            <label class="">建筑面积:</label>
                            <input class="form-control" v-model="workRow.roomtype_srcage" maxlength="20">
                        </div>
                        <div class="form-group col-xs-6">
                            <label class="">套内面积:</label>
                            <input class="form-control" v-model="workRow.roomtype_area" maxlength="20">
                        </div>
                    </div>
                    <div class="row">
                        <div class="form-group col-xs-6">
                            <label class="">户型:</label>
                            <input class="form-control" v-model="workRow.roomtype_spec_name" maxlength="20">
                        </div>
                    </div>
                    <div class="row">
                        <div class="form-group col-xs-12">
                            <label class="">户型图片:</label>
                            <input class="form-control" v-model="workRow.roomtype_plan_pic" maxlength="20">
                        </div>
                    </div>
                </div>
                <div class="modal-footer" style="border-top:none">
                    <button type="button" class="btn btn-primary btn-info" v-on:click="addAct">保存</button>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCARoomTypeControl?method=';

export default {
    props: ['estate', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {}
        }
    },
    name: 'NCAEDRoomTypeControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        estate: function() {
            let _self = this;
            let $roomTypeTable = $('#roomTypeTable');

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer" title="查看户型详情">',
                    value,
                    '</a>'
                ].join('')
            }

            function roomTypeFilter(value) {
                for (let i = 0; i < _self.pagePara.roomType.length; i++) {
                    if (_self.pagePara.roomType[i].id === value) {
                        return _self.pagePara.roomType[i].text
                    }
                }
                return ''
            }

            function getData() {
                _self.$http.post(apiUrl + 'search', {
                    estateId: _self.estate[0].estate_id
                }).then((response) => {
                    let retdata = response.data.info;
                    $roomTypeTable.bootstrapTable('load', {
                        data: retdata
                    })
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }

            window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {

                    _self.$router.push({
                        path: '/nca/baseconfig/NCARoomTypeDetailControl',
                        query: {
                            roomTypeId: row.roomtype_id
                        }
                    })
                }
            };

            function queryParams(params) {
                params.estateId = _self.estate.estate_id;
                return JSON.stringify(params);
            }

            function initTable() {
                $roomTypeTable.bootstrapTable('destroy');
                $roomTypeTable.bootstrapTable({
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
                        common.BTRowFormatWithFEL('roomtype_name', '户型名称', nameFormatter, tableEvents),
                        common.BTRowFormatEditable('roomtype_room_count', '房型'),
                        common.BTRowFormatEditableRight('roomtype_srcage', '建筑面积'),
                        common.BTRowFormatEditableRight('roomtype_area', '套内面积'),
                        common.BTRowFormatEditable('roomtype_spec_name', '户型类别'),
                    ],
                    idField: 'roomtype_id',
                    uniqueId: 'roomtype_id',
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    height: 400,
                    onEditableSave: async function(field, row, oldValue, $el) {
                        let response = await _self.$http.post(apiUrl + 'modify', row);
                    }
                })
                common.changeTableClass($roomTypeTable)
            }
            initTable();
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#AddModal').modal('show')
        },
        addAct: async function(event) {
            let _self = this;
            //name,type,acreage,describe
            _self.workRow.estate_id = _self.estate.estate_id;

            _self.$http.post(apiUrl + 'add', _self.workRow).then((response) => {
                let retData = response.data.info;
                $('#roomTypeTable').bootstrapTable("refresh");
                _self.workRow = {};
                common.dealSuccessCommon('增加成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })

            $('#AddModal').modal('hide')
        },
        syncAct: async function(event) {
          let _self = this;
          try {
              let response = await _self.$http.post(apiUrl + 'sync', {estate_id: _self.estate.estate_id});
              $('#roomTypeTable').bootstrapTable("refresh");
              common.dealSuccessCommon('同步成功');
          } catch (error) {
              common.dealErrorCommon(_self, error);
          }
        }
    }
}
</script>
<style scoped>
#roomTypeToolbar {
    padding-bottom: 10px;
}
</style>
