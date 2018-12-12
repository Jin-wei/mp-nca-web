<!--户型验收项-->
<template>
<div>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <button id="addM" class="btn btn-info btn-xs" @click="addM">新建</button>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">验收文档</h4>
            </div>
            <div>
                <div class="panel-body auto-height">
                    <div id="toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="search_gonode_id">
                                        <option value="">请选择工序</option>
                                        <option v-for="state in roomType.nodesInfo" v-bind:value="state.id">
                                            {{state.text}}
                                        </option>
                                    </select>
                            </div>
                            <div class="form-group ">
                                <button class="btn btn-info" v-on:click="search">
                                        <i class="fa fa-search"></i>
                                    </button>
                            </div>
                        </div>
                    </div>
                    <div class="btn-group" style="margin-top: 10px;margin-bottom: 10px">
                        <button type="button" class="btn btn-white" v-for="room in roomType.roomsInfo" v-bind:class="{active: selectRoomId == room.id}" @click="roomTypeSwitch(room.id)">{{room.text}}</button>
                    </div>
                    <table id="propertyAcceptanceTable"></table>
                </div>
            </div>
            <div class="modal fade" id="propertyAcceptanceModal">
                <div class="modal-dialog">
                    <div class="modal-content">
                        <div class="modal-header">
                            <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                            <h4 v-if="!workRow.goacceptance_id" class="modal-title">新建验收项</h4>
                            <h4 v-else class="modal-title">编辑验收项</h4>
                        </div>
                        <form @submit.prevent="submitForm" id="formYSX">
                            <div class="modal-body row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>验收编号</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.goacceptance_index" data-parsley-maxlength="50" data-parsley-type="integer" data-parsley-required="true">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>验收项</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.goacceptance_name" data-parsley-required="true" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>施工工序</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="gonode_id" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>空间</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="goroom_id" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>是否隐藏</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="gois_hidden" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span class="table-required">*</span>上传格式</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="goupload_format" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-12" style="padding-left: 6px">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>采集证据要求</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" v-model="workRow.goevidence_require" maxlength="500" data-parsley-maxlength="500" data-parsley-required="true"></textarea>
                                    </div>
                                </div>
                                <div class="form-group col-sm-12" style="padding-left: 6px">
                                    <label class="col-sm-2 control-label"><span class="table-required">*</span>工艺要求</label>
                                    <div class="col-sm-10">
                                        <textarea class="form-control" v-model="workRow.gotechnological_require" maxlength="500" data-parsley-maxlength="500" data-parsley-required="true"></textarea>
                                    </div>
                                </div>
                                <div class="form-group col-sm-12">
                                    <div style="margin-left: 31px">
                                        <label class="btn btn-info btn-xs fileupload-button">上传实例
                                                <input id='goacceptanceUpload' type="file" name="file">
                                            </label>
                                    </div>
                                </div>
                                <div class="form-group col-sm-12">
                                    <div class="image-div" style="margin-left: 31px">
                                        <div class="image-content" v-for="(url, index) in uploadImages">
                                            <img class="image-display" :src="url">
                                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-danger delete-btn" @click="removImage(index)">
                                                    <i class="fa fa-times"></i>
                                                </a>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="modal-footer">
                                <button type="submit" v-if="workRow.goacceptance_id" class="btn btn-info">修改
                                    </button>
                                <button type="submit" v-else="" class="btn btn-info">确认
                                    </button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCARoomTypeDetailControl?method=';

function imageArrayToString(array) {
    return array.length > 0 ? array.join('|') : ''
}

function stringToArray(string) {
    return string.length > 0 ? string.split('|') : []
}

export default {
    props: ['roomType', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            uploadImages: [], //记录上传图片的url,用于显示
            files: [], //记录上传图片的file信息，Object类型
            selectRoomId: ''
        }
    },
    name: 'RTAcceptanceHQControl',
    watch: {
        pagePara: function() {
            let _self = this;
            common.initSelect2($('#gois_hidden'), _self.pagePara.tfInfo);
            common.initSelect2($('#goupload_format'), _self.pagePara.acceptanceInfo);
        },
        roomType: function() {
            let _self = this
            let $table = $('#propertyAcceptanceTable');
            if(_self.roomType.roomsInfo && _self.roomType.roomsInfo.length>0){
                _self.selectRoomId = _self.roomType.roomsInfo[0].id
            }
            function queryParams(params) {
                common.initImageViewer()
                if ($('#search_gonode_id').val()) {
                    params.gonode_id = $('#search_gonode_id').val()
                }
                params.roomtype_id = common.getUrlParams("roomTypeId")
                params.goroom_id = _self.selectRoomId;
                return JSON.stringify(params);
            }

            function editFormatter(value, row) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 tableEdit btn-primary">编辑</a>' +
                    '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
                ].join('')
            }

            function nodeTypeFilter(value) {
                for (let i = 0; i < _self.roomType.nodesInfo.length; i++) {
                    if (_self.roomType.nodesInfo[i].id == value) {
                        return _self.roomType.nodesInfo[i].text
                    }
                }
                return ''
            }

            function roomTypeFilter(value) {
                for (let i = 0; i < _self.roomType.roomsInfo.length; i++) {
                    if (_self.roomType.roomsInfo[i].id === value) {
                        return _self.roomType.roomsInfo[i].text
                    }
                }
                return ''
            }

            function acceptanceFormatter(value, row, index) {
                let images = row.images
                let retString = '<div class="form-inline image" role="form">'
                for (let i = 0; i < images.length; i++) {
                    let url = images[i].file_url
                    retString += '<sapn><img class="icon-size" src="' + url + '"></sapn>'
                }
                retString += '</div>'
                return retString
            }

            function initTable() {
                window.tableEvents = {
                    'click .tableDelete': function(e, value, row, index) {
                      common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_a', $table, row, 'goacceptance_id', function() {})
                    },
                    'click .tableEdit': function (e, value, row, index) {
                        _self.oldRow = $.extend(true, {}, row)
                        _self.workRow = $.extend(true, {}, row)
                        $('#gonode_id').val(_self.workRow.gonode_id).trigger('change')
                        $('#goroom_id').val(_self.workRow.goroom_id).trigger('change')
                        $('#gois_hidden').val(_self.workRow.gois_hidden).trigger('change')
                        $('#goupload_format').val(_self.workRow.goupload_format).trigger('change')
                        _self.uploadImages = []
                        for (let i = 0; i < _self.workRow.images.length; i++) {
                            let image = _self.workRow.images[i]
                            _self.uploadImages.push(image.file_url)
                        }
                        _self.files = _self.workRow.images
                        $('#propertyAcceptanceModal').modal('show')
                    }
                };
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_a',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('goacceptance_index', '验收编号'),
                        common.BTRowFormatWithFormatterAlignLeft('gonode_id', '施工工序', nodeTypeFilter),
                        common.BTRowFormatWithFormatterAlignLeft('goroom_id', '空间', roomTypeFilter),
                        common.BTRowFormatAlignLeft('goacceptance_name', '验收项'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'gois_hidden', '隐蔽', 'tfInfo'),
                        common.BTRowFormat('gotechnological_require', '工艺要求'),
                        common.BTRowFormat('goevidence_require', '采集证据要求'),
                        common.BTRowFormatEdSelect2Disabled(_self, 'goupload_format', '上传格式', 'acceptanceInfo'),
                        common.BTRowFormatWithFormatter('images', '上传实例', acceptanceFormatter),
                        common.actFormatter('edit', editFormatter, tableEvents),
                    ],
                    idField: 'goacceptance_id',
                    uniqueId: 'goacceptance_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    striped: true,
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_a', row, 'goacceptance_id', $table)
                    }
                });
                common.changeTableClass($table)
            }

            async function initPage() {
                common.initSelect2($('#gonode_id'), _self.roomType.nodesInfo);
                common.initSelect2($('#goroom_id'), _self.roomType.roomsInfo);
                initTable()
            }

            $(function() {
                initPage();
                common.fileUpload(_self, $('#goacceptanceUpload'), apiUrl, function(fileInfo) {
                    _self.uploadImages.push(fileInfo.url)
                    let convert = {}
                    convert.file_name = fileInfo.name
                    convert.file_url = fileInfo.url
                    convert.file_type = fileInfo.type
                    _self.files.push(convert)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            });
        }
    },
    methods: {
        addM: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#gonode_id').val(null).trigger('change')
            $('#goroom_id').val(null).trigger('change')
            $('#gois_hidden').val(null).trigger('change')
            $('#goupload_format').val(null).trigger('change')
            $('#formYSX').parsley().reset()
            _self.uploadImages = []
            _self.files = []
            $('#propertyAcceptanceModal').modal('show')
        },
        submitForm: async function(event) {
            let _self = this;
            let formYSX = $('#formYSX')
            if (formYSX.parsley().isValid()) {
//                _self.workRow.roomtype_id = _self.roomTypeId
                _self.workRow.roomtype_id = common.getUrlParams("roomTypeId")
                _self.workRow.gonode_id = common.getSelect2Val('gonode_id')
                _self.workRow.goroom_id = common.getSelect2Val('goroom_id')
                _self.workRow.gois_hidden = common.getSelect2Val('gois_hidden')
                _self.workRow.goupload_format = common.getSelect2Val('goupload_format')
                _self.workRow.images = _self.files
                try {
                    let apiName = _self.workRow.goacceptance_id ? 'modify_a' : 'add_a'
                    let param = _self.workRow.goacceptance_id ? {
                        'old': _self.oldRow,
                        'new': _self.workRow
                    } : _self.workRow
                    let response = await _self.$http.post(apiUrl + apiName, param);
                    let retData = response.data.info;
                    $('#propertyAcceptanceTable').bootstrapTable("refresh", _self.queryParams);
                    formYSX.parsley().reset();
                    $('#propertyAcceptanceModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        },
        removImage: async function(index) {
            let _self = this
            _self.uploadImages.splice(index, 1)
            _self.files.splice(index, 1)
        },
        roomTypeSwitch: function(id) {
            let _self = this
            _self.selectRoomId = id
            $('#propertyAcceptanceTable').bootstrapTable("refresh")
        },
        search: function() {
            let _self = this;
            $('#propertyAcceptanceTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
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

.mt_10 {
    margin-top: 10px;
}
</style>
