<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="javascript:history.back()">系统管理</a></li>
        <li class="active">消息管理</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">消息管理</h4>
                </div>

                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="messageListTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMessageListControl?method=';

export default {
    data: function() {
        return {
            oldRow: '',
            pagePara: {}
        }
    },
    name: 'NCAMessageListControl',
    mounted: async function() {
        let _self = this;
        let $messageListTable = $('#messageListTable');

        function queryParams(params) {

            return JSON.stringify(params)
        }

        function initTable() {
            window.tableEvents = {
                'click .detail': function(e, value, row, index) {
                    if (row.message_user_type == 1) {
                        _self.$router.push({
                            path: '/nca/baseconfig/NCANoticeReceiveDetailControl',
                            query: {
                                notice_id: row.message_id
                            }
                        })
                    } else if (row.message_user_type == 2) {
                        _self.$router.push({
                            path: '/nca/ordermanage/NCAOrderDetailControl',
                            query: {
                                orderId: row.message_id
                            }
                        })
                    } else if (row.message_user_type == 8) {
                        _self.$router.push({
                            path: '/nca/ordermanage/NCASOrderDetailControl',
                            query: {
                                orderId: row.message_id
                            }
                        })
                    }

                }
            };
            $messageListTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_r',
                queryParams: queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('message_user_title', '消息名称'),
                    common.BTRowFormatAlignLeft('username', '发布人'),
                    common.BTRowFormat('message_start_date', '发布时间'),
                    common.BTRowFormat('message_user_state', '状态'),
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-info btn-xs m-r-5 detail btn-info-delete">查看</a>';
                    }, tableEvents)
                ],
                idField: 'message_user_id',
                uniqueId: 'message_user_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                pageNumber : 1,
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                }
            })
            common.changeTableClass($messageListTable)
        }

        async function initData() {
            await _self.getPagePara();

            initTable();
            $('#messageListTable').parsley();
            common.reSizeCall();
        }
        initData()
    },
    methods: {
        getPagePara: async function(event) {
            let _self = this
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData))
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
