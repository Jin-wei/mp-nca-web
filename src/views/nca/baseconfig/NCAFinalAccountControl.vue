<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">工程项目管理</a></li>
            <li class="active">项目决算列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">项目决算列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="请输入项目编号、项目名称" id="searchText" style="width: 200px"/>
                                </div>
                                <div class="form-group">
                                    <button  class="btn btn-info" v-on:click="searchClick">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCAFinalAccountControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                userinfo: common.getStoreData('userinfo'),
                show_return: 0
            }
        },
        name: 'NCAFinalAccountControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectTable');

            function queryParams(params) {
                params.type = '2'
                if ($('#searchText').val){
                    params.search_text = $('#searchText').val()
                }
                return JSON.stringify(params);
            }

            function nameFormatter(value, row) {
                return [
                    '<a id="project_detail" style="cursor: pointer;">',
                    value,
                    '</a>'
                ].join('')
            }

            function addressFormatter(value, row) {
                /*return row.province + ' ' + row.city + ' ' + row.district + ' ' + row.estate_name*/
                return common.remarkFormatter(row.province + ' ' + row.city + ' ' + row.district + ' ' + row.estate_name);
            }

            function userFormatter(value, row) {
                if (value) {
                    for (let s of _self.pagePara.staffInfo) {
                        if (s.id === value) {
                            return s.text
                        }
                    }
                    return null;
                }else {
                    return null
                }
            }

            function dataFormatter(value, row, index) {
                if (value) {
                    var date = new Date(value);
                    var dateStr = date.toLocaleString();
                    return dateStr;
                }else {
                    return null
                }
            }

            function stateFormatter(value, row, index) {
                if (value) {
                    for (let s of _self.pagePara.projectState) {
                        if (s.id === value) {
                            return s.text
                        }
                    }
                    return null;
                }else {
                    return null
                }
            }

            window.tableEvents = {
                'click #project_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAFinalAccountDetailControl',
                        query: {
                            projectId: row.project_id
                        }
                    })
                }
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
                        common.BTRowFormatWithFE('project_id', '项目编号', nameFormatter, tableEvents),
                        common.BTRowFormatAlignLeft('project_name', '项目名称'),
                        common.BTRowFormatWithFormatterAlignRight('project_budget_amount', '项目预算金额',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('project_quoted_amount', '项目报价金额',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('project_final_amount', '项目决算金额',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignLeft('province', '地址', addressFormatter,common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('project_approver_id', '审批人', userFormatter),
                        common.BTRowFormatWithFormatter('created_at', '创建日期', dataFormatter),
                        common.BTRowFormatWithFormatter('project_state', '决算状态', stateFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('project_final_remark', '备注',common.remarkFormatter)
                    ],
                    sortOrder:'asc',
                    idField: 'project_id',
                    uniqueId: 'project_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'project_id', $table)
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($table)
            }

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info;
                    _self.show_return = _self.$route.query.show_return;
                    console.log('show_return',_self.show_return)
                    $('#distpicker').distpicker({
                        placeholder: true
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            async function initPage() {
                await getInit()
                initTable()
                common.initSelect2($('#project_approver'), _self.pagePara.staffInfo);
                $('#formProject').parsley()
            }

            $(async function() {
                await initPage();
            });
        },
        methods: {
            searchClick: function() {
                let _self = this
                $('#projectTable').bootstrapTable("refresh", _self.queryParams)
            }
        }
    }
</script>
<style scoped>
</style>
