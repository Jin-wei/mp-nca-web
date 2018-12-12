<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:">工程项目管理</a></li>
            <li class="active">工程项目列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">工程项目列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="请输入项目编号、项目名称" id="searchText" style="width: 200px"/>
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="searchClick">
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
    const apiUrl = '/api/nca/baseconfig/NCAProjectControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                userinfo: common.getStoreData('userinfo'),
                show_return: 0,
                project_id:''
            }
        },
        name: 'NCAProjectControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectTable');
            _self.project_id = _self.$route.query.project_id;
            function queryParams(params) {
                params.type = '1'
                if ($('#searchText').val){
                    params.search_text = $('#searchText').val()
                }
                params.project_id = _self.project_id;
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
                        //已经是决算单，预算单的状态全部为已通过
                        if (value === '3' || value === '5' || value === '6' || value === '7') {
                            return '已通过'
                        }
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
                    if(row.project_state==2){
                        _self.$router.push({
                            path: '/nca/baseconfig/NCAProjectDetailNewControl',
                            query: {
                                projectId: row.project_id
                            }
                        })
                    }else {
                        return common.dealPromptCommon('该工程未通过审核');
                    }
                }
            }

            function initTable() {
                let columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('project_id', '项目编号', nameFormatter, tableEvents),
                    common.BTRowFormatAlignLeft('project_name', '项目名称'),
                    common.BTRowFormatWithFormatterAlignRight('project_budget_amount', '项目预算金额',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('project_quoted_amount', '项目报价金额',common.priceFormat),
                    common.BTRowFormatAlignLeft('province', '地址', addressFormatter,common.remarkFormatter),
//                    common.BTRowFormatEdSelect2(_self, 'project_approver_id', '审批人', 'staffInfo'),
                    common.BTRowFormatWithFormatter('created_at', '创建日期', dataFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'project_state', '状态', 'projectProjectState'),
//                    common.BTRowFormatWithFormatter('project_budget_remark', '备注'),
//                    common.BTRowFormatWithFE('', '操作', ()=>{
//                        return [
//                            '<a class="btn btn-info btn-xs m-r-5 deleteProject">删除</a>'
//                        ].join('')
//                    }, tableEvents)
                ]
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
                    columns: columns,
                    sortOrder:'asc',
                    idField: 'project_id',
                    uniqueId: 'project_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
//                    onPostBody: function() {
//                        $("[data-uniqueid]").each(function() {
//                            let actrow = $('#projectTable').bootstrapTable('getRowByUniqueId', this.getAttribute('data-uniqueid'));
//                            console.log('actrow',actrow)
//                            if(actrow){
//                                console.log(actrow.project_state);
//                                if (actrow.project_state != 3) {
//                                    $(this).find('.againSend').each(function() {
//                                        $(this).attr("disabled", true)
//                                    })
//                                }
//                            }
//                        })
//                    },
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        if(row.project_state===2){
                            common.rowModifyWithT(_self, apiUrl + 'modify', row, 'project_id', $table)
                        }else {
                            return common.dealPromptCommon('该工程未通过审核');
                        }

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
                    _self.pagePara = response.data.info
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

            getEstate:async function(event){
                let _self = this;
                $('#estate').html('');
                let params= {
                    province:$('#province').val(),
                    city:$('#city').val(),
                    district:$('#district').val()
                };
                let response = await _self.$http.post(apiUrl + 'getEstate', params);
                let retData = response.data.info;
                common.initSelect2Editable($('#estate'), retData);
            },
            submitProject: function() {
                let _self = this
                if ($('#formProject').parsley().isValid()) {
                    let param = {
                        project_name: $('#project_name').val(),
                        project_estate_id: common.getSelect2Val('estate'),
                        project_approver_id: common.getSelect2Val('project_approver')
                    }
                    _self.$http.post(apiUrl + 'add', param).then((response) => {
                        let retData = response.data
                        if (retData.errno === 0) {
                            common.dealSuccessCommon('项目创建成功')
                            $('#projectTable').bootstrapTable("refresh")
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    },(error) =>{
                        common.dealErrorCommon(_self, error)
                    })
                    $('#createTaskModal').modal('hide')
                }
            },
            searchClick: function() {
                let _self = this
                $('#projectTable').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
