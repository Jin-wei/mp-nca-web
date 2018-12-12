<template>
<div>
    <div v-show="checkState('DESIGNING')">
        <div class="panel panel-inverse">
            <div class="panel-body">
                <div class="form-group">
                    <div class="form-inline">
                        <div class="row" style="border: 1px solid #eee;padding: 20px">
                            <div class="form-group m-r-10">
                                <div class="input-group">
                                    <span class="input-group-addon">总面积</span>
                                    <input :disabled="checkState('SIGNED')" class="form-control" placeholder="请输入总面积" v-model="order.order_house_area">
                                </div>
                            </div>
                            <div class="form-group m-r-10">
                                <div class="input-group">
                                    <span class="input-group-addon">套餐产品</span>
                                    <select :disabled="checkState('SIGNED')" class="form-control select2" id="produce_id"></select>
                                </div>
                            </div>
                            <div class="form-group m-r-10">
                                <div class="input-group">
                                    <span class="input-group-addon">预收订金</span>
                                    <input :disabled="checkState('SIGNED')" class="form-control" placeholder="请输入预收订金" v-model="order.earnest">
                                </div>
                            </div>
                            <div class="form-group m-r-10 pull-right">
                                <button v-if="!checkState('SIGNED')&& userinfo.user_id === order.designer_id" class="btn btn-info btn-sm m-r-5" @click="degsin">设计</button>
                                <button v-if="!checkState('SIGNED')&& userinfo.user_id === order.designer_id" class="btn btn-info btn-sm m-r-5" @click="kujialesync">同步</button>
                                <button v-if="!checkState('SIGNED')&& userinfo.user_id === order.designer_id" class="btn btn-info btn-sm m-r-5" @click="saveOrder">保存</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="panel panel-inverse">
            <div class="panel-heading">
                <div class="panel-heading-btn">
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
                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning" data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                </div>
                <h4 class="panel-title">3D效果图</h4>
            </div>
            <div class="panel-body">
                <table id="VRTable"></table>
            </div>
        </div>
    </div>
    <div v-show="!checkState('DESIGNING')">
        请分配订单人员
    </div>
    <div class="modal fade" id="editModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">编辑3D效果图</h4>
                </div>

                <div class="modal-body">
                    <div class="row">
                        <div class="form-group col-xs-12">
                            <input class="form-control" v-model="nowFileUrl" placeholder="请输入链接">
                        </div>

                        <div class="form-group col-xs-2 pull-right">
                            <button type="button" class="btn btn-info" v-on:click="saveClick()">保存</button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="degsinModal">
        <div class="modal-dialog modal-dialog-full">
            <div class="modal-content modal-content-full">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">设计</h4>
                </div>
                <div class="modal-body">
                    <div id="degsinModalBody">
                        <iframe src="" id="kujialeiframe" frameborder="0" scrolling="no" width="100%" height="100%" style="border:0px;"></iframe>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method=';

export default {
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            userinfo: common.getStoreData('userinfo'),
            oldRow: '',
            uploadFiles: [],
            nowFileUrl: null,
            nowDesignId: null

        }
    },
    name: 'NCADesignControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {},
        order: function() {
            let _self = this;
            let template = $('#produce_id');
            let $fileTable = $('#fileTable');
            let $VRTable = $('#VRTable');

            window.designEvents = {
                'change .fileupload': function(e, value, row, index) {
                    common.fileFileUpload(this, _self, row, apiUrl, 'design_update', $fileTable, 'design_id')
                },
                'click .delete-button': function(e, value, row, index) {
                    common.deleteFiles(this, _self, row, apiUrl, 'delete_file', $fileTable, 'design_id')
                },
                'click .VREdit': function(e, value, row, index) {
                    _self.nowFileUrl = row.file_url;
                    _self.nowDesignId = row.design_id;
                    $('#editModal').modal('show');
                }
            }

            function trueOrFalseTypeFilter(value) {
                if (value == '1') {
                    return '是'
                }
                return '否'
            }

            function initFileTable() {
                function fileActFormatter(value, row, index) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 fileUpload">上传</a>',
                        '<a class="btn btn-info btn-xs m-r-5 fileDelete">删除</a>'
                    ].join('')
                }

                function urlFormatter(value, row) {
                    return [
                        '<a class="download_url">',
                        value,
                        '</a>'
                    ].join('')
                }

                let columns;
                if (_self.checkState('SIGNED')) {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '文档名称'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('require_hidden', '客户可见', trueOrFalseTypeFilter),
                        common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
                    ]
                } else {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '文档名称'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('require_hidden', '客户可见', trueOrFalseTypeFilter),
                        common.BTRowFormatWithFE('files', '文件', common.filesFormatterWithUpload, designEvents)
                    ]
                }

                $fileTable.bootstrapTable('destroy');

                $fileTable.bootstrapTable({
                    columns: columns,
                    idField: 'design_id',
                    uniqueId: 'design_id',
                    clickToSelect: true,
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function() {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($fileTable)
            }

            function dataFormatter(value, row, index) {
                if (value && row.file_url) {
                    var date = new Date(value);
                    var dateStr = date.toLocaleString();
                    return dateStr;
                } else {
                    return null
                }
            }

            function userFormatter(value, row, index) {
                for (let employee of _self.pagePara.employeeInfo) {
                    if (value == employee.id) {
                        return employee.text;
                    }
                }
                return null;
            }

            function initVRTable() {
                function VRFormatter(value, row, index) {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 VREdit">编辑</a>'
                    ].join('')
                }

                $VRTable.bootstrapTable('destroy');
                let vrColumns = []
                if (_self.checkState('SIGNED')) {
                    vrColumns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '效果图名称'),
                        common.BTRowFormatWithFormatter('file_url', '效果图链接', common.linkFormatter),
                        common.BTRowFormatWithFormatter('updated_at', '上传日期', dataFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('create_user_id', '创建人', userFormatter),
                    ]
                } else {
                    vrColumns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '效果图名称'),
                        common.BTRowFormatWithFormatter('file_url', '效果图链接', common.linkFormatter),
                        common.BTRowFormatWithFormatter('updated_at', '上传日期', dataFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('create_user_id', '创建人', userFormatter),
                        common.actFormatter('act', VRFormatter, designEvents)
                    ]
                }

                $VRTable.bootstrapTable({
                    columns: vrColumns,
                    idField: 'design_id',
                    uniqueId: 'design_id',
                    clickToSelect: true,
                    locale: 'zh-CN'
                })
                common.changeTableClass($VRTable)
            }

            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_order_design', {
                        orderId: _self.order.order_id
                    });
                    let retdata = response.data.info;
                    $('#fileTable').bootstrapTable('load', {
                        data: retdata.designs
                    });

                    $('#VRTable').bootstrapTable('load', {
                        data: retdata.vrs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            function initPage() {
                initFileTable()
                initVRTable()
                $('#formMeasure').parsley();
                $('#formVR').parsley();
                common.initSelect2Width($('#produce_id'), _self.pagePara.produceInfo, '151');
                common.initSelect2($('#measure_room'), _self.order.roomsInfo);
                common.initSelect2($('#measure_pillar'), _self.pagePara.tfInfo);
                common.initSelect2($('#has_bay_window'), _self.pagePara.tfInfo);
                common.initSelect2($('#has_downcomer'), _self.pagePara.tfInfo);
                common.initSelect2($('#srv_type'), _self.pagePara.fileTypeInfo);

                template.val(_self.order.produce_id).trigger('change');

                getTablesData(_self);
            }

            initPage();
        }
    },
    methods: {
        degsin: async function(event) {
            let _self = this;
            try {
                let iframePara = {
                    order_id: _self.order.order_id
                }
                let response = await _self.$http.post('/api/openapi/kujiale?method=getIframeSrc', iframePara);
                let body = response.data.info;

                if (window.postMessage) {
                    let callback = async function(ev) {
                        // console ? console.log(ev) : alert(ev.data);
                        if (typeof(ev.data) != "object") {
                            if (ev.origin === 'http://www.kujiale.com' ||
                                ev.origin === 'http://yun.kujiale.com' ||
                                ev.origin === 'https://www.kujiale.com' ||
                                ev.origin === 'https://yun.kujiale.com') {
                                let data = JSON.parse(ev.data)
                                // data.order_id = _self.order.order_id
                                // if (!_self.order.fpid && data.type === 'fp') {
                                //     if (data.action === 'kjl_saved' || data.action === 'kjl_completed') {
                                //         _self.$http.post('/api/openapi/kujiale?method=updateOrder', data).then((response) => {
                                //             let retData = response.data.info;
                                //             _self.order.fpid = retData.fpid
                                //         })
                                //     }
                                // }
                                // if (data.type === 'des') {
                                //     if (data.action === 'kjl_completed' || data.action === 'kjl_saved') {
                                //         _self.$http.post('/api/openapi/kujiale?method=updateOrder', data).then((response) => {
                                //             let retData = response.data.info;
                                //             _self.order.desid = retData.desid
                                //         })
                                //     }
                                // }

                                if ((data.type === 'fp' || data.type === 'des') && data.action === 'kjl_completed') {
                                    $('#kujialeiframe').attr('src', '');
                                    $('#degsinModal').modal('hide')
                                }
                            }
                        }
                    };
                    if ('addEventListener' in document) {
                        window.addEventListener('message', callback, false);
                    } else if ('attachEvent' in document) {
                        window.attachEvent('onmessage', callback);
                    }
                } else {
                    // 如果不支持postMessage， 则使用ie6/7的window共有属性navigator进行hack
                    window.navigator.listenKJL = function(msg) {
                        alert(msg)
                        // var data = JSON.parse(ev.data)
                    };
                }

                $('#degsinModalBody').height($(window).height() - 80)
                $('#kujialeiframe').attr('src', body.iframeurl);
                $('#degsinModal').modal('show')
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        kujialesync: function(event) {
            let _self = this;
            common.dealConfrimCommon('生成后之前的空间和物料将被清空,是否继续操作', async function() {
                try {
                    let response = await _self.$http.post('/api/openapi/kujiale?method=sync', {
                        order_id: _self.order.order_id
                    });
                    _self.$parent.refreshOrder();
                    common.dealSuccessCommon('同步成功');
                } catch (error) {
                    common.promptInformationCommon(_self, error);
                }
            })
        },
        saveOrder: function(event) {
            let _self = this;
            console.log($("#produce_id  option:selected").val(),'_self.save')
            if (!_self.order.earnest) {
                return common.dealWarningCommon('请输入预收订金')
            } else {
                if (parseFloat(_self.order.earnest) < 1000) {
                    return common.dealWarningCommon('预收订金必须大于1000')
                }
            }

            if ($("#produce_id  option:selected").val() === undefined || $("#produce_id  option:selected").length === 0) {
                return common.dealWarningCommon('请选择套餐产品！')
            }

            _self.order.produce_id = $("#produce_id  option:selected").val();
            _self.$http.post(apiUrl + 'saveOrder', _self.order).then((response) => {
                let retData = response.data.info;
                _self.$parent.refreshOrder();
                $('#table').bootstrapTable('updateByUniqueId', {
                    id: _self.order.order_id,
                    row: _self.order
                })
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        },
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        saveClick: function() {
            let _self = this;
            _self.$http.post(apiUrl + 'edit_file_url', {
                fileUrl: _self.nowFileUrl,
                designId: _self.nowDesignId
            }).then((response) => {
                $('#editModal').modal('hide');
                _self.$parent.refreshOrder();
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        }
    }
}
</script>
<style scoped>
.modal-dialog-full {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
}

.modal-content-full {
    height: auto;
    min-height: 100%;
    border-radius: 0;
}

.modal-body-full {
    height: auto;
    min-height: 100%;
    border-radius: 0;
}
</style>
