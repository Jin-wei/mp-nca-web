<template>
<div class="panel panel-inverse">
    <!--功能-->
    <div class="panel-body" style="padding-top: 0px;">
        <div>
            <div id="roomNumToolbar" style="padding-bottom: 0px">
                <div class="form-inline" role="form" style="padding:0px;">
                    <!--<div class="form-group">-->
                    <div class="form-group">
                        <select class="form-control select2" multiple id="build"></select>
                    </div>
                    <div class="form-group" style="margin-left: 5px">
                        <select class="form-control select2" multiple id="unit"></select>
                    </div>
                    <div class="form-group">
                        <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                            <i class="fa fa-search"></i>
                        </button>
                    </div>
                    <div class="form-group pull-right" v-show="!pagePara.thirdUser">
                        <button id="addM" class="btn btn-info" v-on:click="addM">增加
                        </button>
                        <button id="importM" class="btn btn-info" v-on:click="importM">导入
                        </button>
                        <button id="downloadM" class="btn btn-info" v-on:click="downloadM">下载模板
                        </button>
                        <button id="createOrderM" class="btn btn-info" v-on:click="createOrderM">批量产生订单
                        </button>
                    </div>
                    <!--</div>-->
                </div>
            </div>
            <table id="roomNumTable"></table>
        </div>
    </div>
    <!--新增房号-->
    <div class="modal fade" id="AddModalNum">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增房屋</h4>
                </div>
                <form @submit.prevent="addAct" id="formAddM">
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>栋</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.build" data-parsley-maxlength="3" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>单元</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.unit" data-parsley-maxlength="2" data-parsley-required="true">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>房号</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="workRow.room_no" data-parsley-maxlength="4" data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>原始户型</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="roomTypeNumAdd" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

    <div class="modal fade" id="ImportModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">导入房间</h4>
                </div>

                <div class="modal-body">
                    <div class="form-group col-md-12">
                        <label>选择文件：</label><span>（请选.csv文件）</span>
                        <div>
                            <input type="file" id="file" class="form-control input-sm " accept=".csv" size="1">
                            <input type="text" id="select1" hidden>
                            <!--<span class="margin-top-ten">{{filePath}}</span>-->
                        </div>
                    </div>

                </div>
                <div class="modal-footer" style="border-top:none">
                    <button type="button" class="btn btn-primary btn-info" id="importAct" v-on:click="importAct"><i class="fa fa-fw fa-plus"></i>导入</button>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEstateControl?method=';

export default {
    props: ['estate', 'pagePara'],
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            workRow: {},
            oldRow: '',
            fileUrl: "",
            filePath: ""
        }
    },
    name: 'NCAEDRoomNumControl',
    watch: {
        estate: function() {
            let _self = this;
            let $roomNumTable = $('#roomNumTable');

            function actFormatter(value, row) {
                console.log(1234)
                if(row.order_id){
                    return [].join('')
                }else{
                    return [
                        '<a class="btn btn-primary btn-xs m-r-5 update_estate btn-info-table">编辑</a>'
                    ].join('')
                }

            }

            function initData() {
                common.initSelect2Placeholder($('#build'), _self.estate.build, '请选择栋');
                common.initSelect2Placeholder($('#unit'), _self.estate.unit, '请选择单元');
                common.initSelect2($('#roomTypeNumAdd'), _self.estate.roomtype);

                initTable();
                $('#formAddM').parsley()

                common.reSizeCall();
            }

            function queryParams(params) {
                params.estate_id = _self.estate.estate_id;
                params.build = common.getSelect2Val('build');
                params.unit = common.getSelect2Val('unit');
                return JSON.stringify(params)
            }

            function orderFormatter(value, row) {
                return [
                    '<a class="order_detail">',
                    value,
                    '</a>'
                ].join('')
            }

            function initTable() {
                window.tableEvents = {
                    'click .update_estate': function(e, value, row, index) {
                        _self.workRow = JSON.parse(JSON.stringify(row));
                        _self.roomId = row.room_id;

                        $('#roomTypeNumAdd').val(row.roomtype_id).trigger('change');
                        $('#AddModalNum').modal('show')
                    },
                    'click .order_detail': function(e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAGOrderDetailControl',
                            query: {
                                orderId: row.order_id
                            }
                        })
                    }
                };
                let columsArr = [];
                if (_self.userinfo.domain_type == 0) {
                    columsArr = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('room_code', '房屋ID'),
                        common.BTRowFormat('build', '栋'),
                        common.BTRowFormat('unit', '单元'),
                        common.BTRowFormat('room_no', '房间号'),
                        common.BTRowFormat('roomtype_name', '原始格局'),
                        common.BTRowFormatWithFE('order_id', '订单号', orderFormatter, tableEvents),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                } else {
                    columsArr = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('room_code', '房屋ID'),
                        common.BTRowFormat('build', '栋'),
                        common.BTRowFormat('unit', '单元'),
                        common.BTRowFormat('room_no', '房间号'),
                        common.BTRowFormat('roomtype_name', '原始格局'),
                        common.BTRowFormatWithFE('order_id', '订单号', orderFormatter, tableEvents),
                    ]
                }

                $roomNumTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'searchRoomList',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columsArr,
                    idField: 'room_id',
                    uniqueId: 'room_id',
                    toolbar: '#toolbar',
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN'
                });
                common.changeTableClass($roomNumTable)
            }

            $('#file').change(function() {
                let files = this.files;
                let formData = new FormData();
                if(files.length>0){
                    formData.append('file', files[0]);
                    _self.filePath = $('#file').val();
                    _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.fileUrl = response.data.info.uploadurl.url;
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })
                }
            });
            initData();
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#roomTypeNumAdd').val(null).trigger('change');
            $('#formAddM').parsley().reset()
            $('#AddModalNum').modal('show')
        },
        importM: function(event) {
            let _self = this;
            $('#ImportModal').modal('show')
        },
        importAct: function(event) {
            let _self = this;
            if (!_self.fileUrl) {
                return common.dealWarningCommon('请选择文件')
            }
            var params = {
                uploadurl: _self.fileUrl,
                estate_id: _self.estate.estate_id,
                estate_no:_self.estate.estate_no
            }
            _self.$http.post(apiUrl + 'importRoom', params).then((response) => {
                let retData = response.data.info;
                $('#roomNumTable').bootstrapTable("refresh");
                common.dealSuccessCommon('保存成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });

            $('#ImportModal').modal('hide')
        },
        addAct: async function(event) {
            let _self = this;
            try {
                if ($('#formAddM').parsley().isValid()) {
                    _self.workRow.roomtype_id = common.getSelect2Val('roomTypeNumAdd')
                    if (!_self.workRow.room_id) {
                        _self.workRow.estate_id = _self.estate.estate_id
                        await _self.$http.post(apiUrl + 'addRoom', _self.workRow)
                    } else {
                        await _self.$http.post(apiUrl + 'modifyRoom', _self.workRow);
                    }
                    $('#roomNumTable').bootstrapTable("refresh");
                    $('#AddModalNum').modal('hide')
                }

            } catch (error) {
                common.dealErrorCommon(_self, error)
            }

        },
        queryConfirm: function(event) {
            $('#roomNumTable').bootstrapTable("refresh")
        },
        downloadM: function(event) {
            let _self = this;
            _self.$http.post(apiUrl + 'downloadTemplate', {}).then((response) => {
                let downUrl = response.data.info;
                window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        createOrderM: function(event) {
            let _self = this;
            common.dealConfrimCommon('确认要批量生成订单', function() {
                _self.$http.post(apiUrl + 'createOrder', {
                    estate_id: _self.estate.estate_id
                }).then((response) => {
                    $('#roomNumTable').bootstrapTable("refresh");
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            })
        }
    }
}
</script>
<style scoped>
#search_div input {
    width: 200px;
}

.select-width {
    width: 110px;
}

.margin-top-ten {
    margin-top: 10px;
}

.image-div {
    margin-left: 20px
}

.image-content {
    width: 60px;
    position: relative;
    float: left;
}

.image-display {
    border: 1px solid #0fd16f;
    width: 50px;
    height: 50px;
    margin-right: 10px;
    margin-bottom: 10px;
}

.delete-btn {
    position: absolute;
    top: -7px;
    right: 2px;
}

#roomNumToolbar {
    padding-bottom: 10px;
}
</style>
