<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">闲置库存申请</a></li>
            <li class="active">闲置库存申请单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                        </div>
                        <h4 class="panel-title">闲置库存申请单</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text_apply" placeholder="搜索闲置库存申请单号" style="width: 230px;">
                                </div>
                                <div class="form-group">
                                    <select class="form-control select2" multiple id="apply_state"></select>
                                </div>
                                <div class="form-group">
                                    <button id="searchApply" class="btn btn-info" v-on:click="queryConfirm">
                                        <i class="fa fa-search"></i>
                                    </button>
                                    <button class="btn btn-info" v-on:click="addApply" style="display: none">
                                        <i class="glyphicon glyphicon-plus"></i> 新增
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="idleApplyTable"></table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/inventorymanage/NCAIdleApplyControl?method=';

    export default {
        data: function() {
            return {
                pagePara:{}
            }
        },
        name: 'NCAIdleApplyControl',
        mounted: function() {
            let _self = this;
            let $idleApplyTable = $('#idleApplyTable');

            async function initData() {
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));
                common.initSelect2Placeholder($('#apply_state'), retData.idleApplyStateType, '状态');
                initIdleTable();
                common.reSizeCall();
            }

            function nameFormatter(value, row) {
                return [
                    '<a class="show_detail" style="cursor: pointer" title="查看">',
                    value,
                    '</a>'
                ].join('')
            }
            function queryParams(params) {
                params.search_text = $('#search_text_apply').val();
                params.idle_apply_state = common.getSelect2Val('apply_state');
                return JSON.stringify(params)
            }

              window.tableEvents = {
                'click .show_detail': function(e, value, row, index) {
                  _self.$router.push({
                    path: '/nca/inventorymanage/NCAIdleApplyDetailControl',
                    query: {
                      idleapply_id: row.idleapply_id
                    }
                  })
                }
              };

            function initIdleTable() {
                let colums = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('idleapply_id', '闲置库存申请单号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('apply_submit_name', '申请人'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'idle_apply_state', '申请单状态', 'idleApplyStateType'),
                    common.BTRowFormatAlignLeft('apply_review_name', '审批人'),
                    common.BTRowFormat('idle_apply_review_date', '审批时间'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormatWithFormatterAlignLeft('idle_apply_remark', '驳回说明',common.remarkFormatter)
                ];
                $idleApplyTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: colums,
                    idField: 'idleapply_id',
                    uniqueId: 'idleapply_id',
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
                    }
                });
                common.changeTableClass($idleApplyTable)
            }

            $(function() {
                initData();
            })
        },
        methods: {
            queryConfirm: function(event) {
                let _self = this;
                $('#idleApplyTable').bootstrapTable("refresh")
            },
            addApply:async function () {
              let _self = this;
              let params = {}
              _self.$http.post(apiUrl + 'add', params).then(response => {
                $('#idleApplyTable').bootstrapTable("refresh");
              }, (response) => {
                common.dealErrorCommon(this, response);
              });
            }
        }
    }
</script>
<style scoped>
</style>