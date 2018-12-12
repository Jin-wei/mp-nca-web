<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">总部管理</a></li>
        <li class="active">公告管理</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">公告管理</h4>
                </div>

                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" id="created_at" placeholder="开始时间"/>
                            </div>
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索公告名称" id="notice_title" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryConfirm" class="btn btn-info" v-on:click="queryConfirm">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="noticeListTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCANoticeControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            apiName: common.getApiName(apiUrl),
            workRow: {},
            oldRow: '',
            pagePara: {}
        }
    },
    name: 'NCANoticeListControl',
    mounted: async function() {
        let _self = this;
        let $table = $('#noticeListTable');

        function queryParams(params) {
            let domain_id = common.getSelect2Val('domain_select')
            if (domain_id) {
                params.domain_id = domain_id
            }
            if($('#created_at').val()){
                params.created_at = $('#created_at').val()
            }
            params.notice_title = $('#notice_title').val();
            return JSON.stringify(params)
        }

        function initTable() {
            window.tableEvents = {
                'click .detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCANoticeReceiveDetailControl',
                        query: {
                            notice_id: row.notice_id
                        }
                    })
                }
            };
            $table.bootstrapTable({
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
                    common.BTRowFormatAlignLeft('notice_title', '公告名称'),
                    common.BTRowFormatAlignLeft('username', '发布人'),
//                    common.BTRowFormatEdSelect2Disabled(_self, 'follow', '发布人', 'genderInfo'),
                    common.BTRowFormat('create_date', '发布时间'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'read_state', '状态', 'noticeInfo'),//未读，已读
                    common.actFormatter('act', () => {
                        return '<a class="btn btn-primary btn-xs m-r-5 detail btn-info-delete">查看</a>';
                    }, tableEvents)
                ],
                idField: 'notice_id',
                uniqueId: 'notice_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    common.DynamicEditableByDomain(_self, $('#noticeListTable'))
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'notice_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            $('#domain_select').on('change', function(evt) {
                _self.queryConfirm()
            });
            common.initSelect2($('#follow'), _self.pagePara.genderInfo)

            initTable();
            common.initDatepicker($('#created_at'));
            $('#noticeListTable').parsley()
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
        },
        queryConfirm: function(event) {
            $('#noticeListTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
