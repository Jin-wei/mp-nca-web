<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">系统管理</a></li>
        <li class="active">积分规则配置</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">积分规则配置列表</h4>
                </div>
                <div class="panel-body">
                    <div class="table-btn" style="float: left">积分规则</div>
                    <div style="padding-bottom: 5px;" class="pull-right">
                    </div>
                    <table id="pointTable"></table>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAPointTypeControl?method=';

export default {
    data: function() {
        return {
            workRow: {},
            oldRow: '',
            pagePara: {}
        }
    },
    name: 'NCAPointTypeControl',
    mounted: async function() {
        let _self = this;
        let $table = $('#pointTable');

        function queryParams(params) {
            params.search_text = $('#customerpoint_name').val();
            return JSON.stringify(params)
        }

        function initTable() {
            $table.bootstrapTable({
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
                    common.BTRowFormatAlignLeft('customerpoint_name', '积分事件'),
                    common.BTRowFormatEditableRight('base_point', '基础积分'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('pointtype_remarks', '备注',570,136)
                ],
                sortOrder:'asc',
                idField: 'pointtype_id',
                uniqueId: 'pointtype_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                },
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    if(field == 'base_point'){
                        let reg = /^[+]{0,1}(\d+)$/;
                        if (!reg.test(row.base_point)) {
                            $table.bootstrapTable('updateByUniqueId', {id: row.pointtype_id, row: _self.oldRow});
                            return common.dealPromptCommon('只能输入大于0的数字');
                        }
                    }
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'pointtype_id', $table)
                }
            })
            common.changeTableClass($table)
        }

        async function initData() {
            await _self.getPagePara()
            initTable();
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
            $('#pointTable').bootstrapTable("refresh")
        }
    }
}
</script>
<style scoped>
</style>
