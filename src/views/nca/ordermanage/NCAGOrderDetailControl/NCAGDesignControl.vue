<!--大客户订单设计文档-->
<template>
<div>
    <div>
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
                <h4 class="panel-title">VR效果图</h4>
            </div>
            <div class="panel-body">
                <table id="VRTable"></table>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/ordermanage/NCAGOrderDetailControl?method=';

async function getTablesData(_self) {
    try {
        var param = {
            roomtype_id: _self.order.roomtype_id
        }
        let response = await _self.$http.post(apiUrl + 'search_design', param);
        let retdata = response.data.info;

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
    props: ['order', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            userinfo: common.getStoreData('userinfo'),
            oldRow: ''
        }
    },
    name: 'NCAGDesignControl',
    mounted: function() {
        let _self = this;
    },
    watch: {
        pagePara: function() {},
        order: function() {
            let _self = this;

            let $fileTable = $('#fileTable');
            let $VRTable = $('#VRTable');

            function trueOrFalseTypeFilter(value) {
                for (let i = 0; i < _self.pagePara.tfInfo.length; i++) {
                    if (_self.pagePara.tfInfo[i].id === value) {
                        return _self.pagePara.tfInfo[i].text
                    }
                }
                return ''
            }

            function initFileTable() {
                $fileTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('file_name', '文档名称'),
                        common.BTRowFormatWithFormatterAlignLeft('file_content', '文档内容描述',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('file_url', '文件', common.imageViewerFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('file_visible', '客户可见', trueOrFalseTypeFilter),
                        common.BTRowFormat('created_time', '创建日期'),
                        common.BTRowFormat('file_creator', '创建人'),
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
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                })
                common.changeTableClass($fileTable)
                common.initImageViewer()
            }

            function initVRTable() {
                $VRTable.bootstrapTable({
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('govr_name', '效果图名称'),
                        common.BTRowFormatWithFormatter('govr_url', '效果图链接', common.linkFormatter),
                        common.BTRowFormat('created_time', '创建日期'),
                        common.BTRowFormat('govr_creator', '创建人'),
                    ],
                    idField: 'govr_id',
                    uniqueId: 'govr_id',
                    clickToSelect: true,
                    locale: 'zh-CN'
                })
                common.changeTableClass($VRTable)
            }

            function initPage() {
                initFileTable()
                initVRTable()
            }

            $(function() {
                initPage();
            });

            getTablesData(_self);
        }
    },
    methods: {
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        }
    }
}
</script>
<style scoped>
</style>
