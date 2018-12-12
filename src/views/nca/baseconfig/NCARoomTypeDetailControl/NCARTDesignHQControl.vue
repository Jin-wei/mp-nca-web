<!--户型设计图-->
<template>
<div>
    <div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <div class="btn-group">
                        <button class="btn btn-info btn-xs fileupload-button" v-on:click="addFile">上传文件
                            </button>
                    </div>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">设计文档</h4>
            </div>
            <div class="panel-body">
                <table id="fileTable"></table>
            </div>
        </div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
                    <div class="btn-group">
                        <button class="btn btn-info btn-xs" v-on:click="addVR">新建</button>
                    </div>
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">VR效果图</h4>
            </div>
            <div class="panel-body">
                <table id="VRTable"></table>
            </div>
        </div>
    </div>
    <div class="modal fade" id="addFileModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">上传文档</h4>
                </div>
                <div class="modal-body">
                    <div class="form-horizontal">
                        <div class="form-group">
                            <label class="col-md-2 control-label">文档描述</label>
                            <div class="col-md-9">
                                <input class="form-control" id="file_content" maxlength="100" data-parsley-maxlength="100">
                            </div>
                        </div>
                        <div class="form-group">
                            <label class="col-md-2 control-label">文档类型</label>
                            <div class="col-md-9">
                                <select class="form-control select2" id="srv_type"></select>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <fileupload target="/api/nca/baseconfig/NCARoomTypeDetailControl?method=upload_d" action="POST" v-on:progress="progress" v-on:start="startUpload" v-on:finish="finishUpload" v-on:fail="failUpload">
                    </fileupload>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="addVRModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新建VR效果图</h4>
                </div>
                <form @submit.prevent="addVRAct" id="formVR">
                    <div class="modal-body">
                        <div class="form-horizontal">
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>效果图名称</label>
                                <div class="col-md-10">
                                    <input class="form-control" v-model="workRow.govr_name" data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                                </div>
                            </div>
                            <div class="form-group">
                                <label class="col-md-2 control-label"><span class="table-required">*</span>效果图链接</label>
                                <div class="col-md-10">
                                    <input type="url" class="form-control" v-model="workRow.govr_url" data-parsley-required="true" data-parsley-type="url">
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">提交</button>
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
import fileupload from '../../../../components/common/fileUpload.vue'

function generateUploadFile(_self, fileInfo) {
    if (_self) {
        _self.workRow = {};
        _self.workRow.srv_id = common.getUrlParams("roomTypeId");
        _self.workRow.file_name = fileInfo.name;
        _self.workRow.file_url = fileInfo.url;
        _self.workRow.file_type = fileInfo.type;
        _self.workRow.file_visible = '1';
        _self.workRow.srv_type = common.getSelect2Val('srv_type')
        _self.workRow.file_content = $('#file_content').val()
        _self.$http.post(apiUrl + 'add_f', _self.workRow).then((response) => {
            let retData = response.data.info;
            common.dealSuccessCommon('文件上传成功')
            $('#fileTable').bootstrapTable('insertRow', {
                index: 0,
                row: retData
            })
            getTablesData(_self)
        }, (response) => {
            common.dealErrorCommon(_self, response)
        })
    }
}

async function getTablesData(_self) {
    try {
        let param = {
            roomtype_id: common.getUrlParams("roomTypeId")
        }
        let response = await _self.$http.post(apiUrl + 'search_f', param);
        let retdata = response.data.info

        $('#fileTable').bootstrapTable('load', {
            data: retdata.files
        });

        $('#VRTable').bootstrapTable('load', {
            data: retdata.vrs
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
            oldRow: '',
            order: {}
        }
    },
    components: {
        fileupload
    },
    name: 'RTDesignHQControl',
    mounted: function() {},
    watch: {
        pagePara: function() {
            let _self = this;
            let $fileTable = $('#fileTable');
            let $VRTable = $('#VRTable');

            common.initSelect2($('#srv_type'), _self.pagePara.fileTypeInfo);

            function trueOrFalseTypeFilter(value, row) {
                if (_self.pagePara.tfInfo) {
                    for (let i = 0; i < _self.pagePara.tfInfo.length; i++) {
                        if (_self.pagePara.tfInfo[i].id === value) {
                            return _self.pagePara.tfInfo[i].text
                        }
                    }
                } else {
                    return ''
                }
            }

            function fileActFormatter(value, row, index) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 fileDelete btn-info-delete">删除</a>'
                ].join('')
            }
            window.designEvents = {
                'click .fileDelete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '文件删除', apiUrl + 'delete_f', $fileTable, row, 'file_id', function() {})
                },
                'click .VRDelete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, 'VR效果图删除', apiUrl + 'delete_v', $('#VRTable'), row, 'govr_id', function() {})
                }
            }

            function initFileTable() {
                $fileTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('file_name', '文档名称'),
                        common.BTRowFormatEdTextareaWidthAlignLeft('file_content', '文档内容描述',120,7),
                        common.BTRowFormatWithFormatter('file_url', '文件', common.linkFormatter),
                        common.BTRowFormatEdSelect2(_self, 'file_visible', '客户可见', 'tfInfo'),
                        common.BTRowFormat('created_time', '创建日期'),
                        common.BTRowFormatAlignLeft('file_creator', '创建人'),
                        common.actFormatter('act', fileActFormatter, designEvents)
                    ],
                    idField: 'file_id',
                    uniqueId: 'file_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_f', row, 'file_id', $fileTable)
                    }
                });
                common.changeTableClass($fileTable)
            }

            function initVRTable() {
                function VRFormatter(value, row, index) {
                    return [
                        '<a class="btn btn-primary btn-xs m-r-5 VRDelete btn-info-delete">删除</a>'
                    ].join('')
                }

                $VRTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEditableLeft('govr_name', '效果图名称'),
                        common.BTRowFormatWithFormatter('govr_url', '效果图链接', common.linkFormatter),
                        common.BTRowFormat('created_time', '创建日期'),
                        common.BTRowFormatAlignLeft('govr_creator', '创建人'),
                        common.actFormatter('act', VRFormatter, designEvents)
                    ],
                    idField: 'govr_id',
                    uniqueId: 'govr_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_v', row, 'govr_id', $VRTable)
                    }
                })
                common.changeTableClass($VRTable)
            }

            initFileTable()
            initVRTable()
            $('#formVR').parsley()
            getTablesData(_self)
        },
        roomType: function() {
            let _self = this;
            getTablesData(_self);
        }
    },
    methods: {
        addFile: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#addFileModal').modal('show')
        },
        addVR: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#formVR').parsley().reset();
            $('#addVRModal').modal('show')
        },
        addVRAct: function(event) {
            let _self = this
            if ($('#formVR').parsley().isValid()) {
                _self.workRow.roomtype_id = common.getUrlParams("roomTypeId");
                _self.$http.post(apiUrl + 'add_v', _self.workRow).then((response) => {
                    let retData = response.data.info;
                    $('#VRTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    });
                    _self.workRow = {};
                    $('#addVRModal').modal('hide')
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }
        },
        //upload component callback
        startUpload(e) {
            // file upload start event
        },
        finishUpload(fileInfo) {
            // file upload finish event
            let _self = this
            let srv_type = common.getSelect2Val('srv_type');
            if (!srv_type) {
                return common.dealWarningCommon('请选择文档类型')
            }
            generateUploadFile(_self, fileInfo)
        },
        failUpload(message) {
            common.dealWarningCommon(message)
        },
        progress(e) {
            // file upload progress
            // returns false if progress is not computable
        }
    }
}
</script>
<style scoped>
</style>
