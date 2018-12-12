<template>
    <div>
        <!--&lt;!&ndash; begin breadcrumb &ndash;&gt;-->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">工程项目管理</a></li>
            <li class="active">工程项目详情</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">工程项目详情</h4>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="form-group col-sm-6">
                            <label class="control-label">项目编号: </label>
                            <span>{{projectId}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">项目名称: </label>
                            <span>{{projectDetail.project_name}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">地址: </label>
                            <span>{{projectDetail.province + ' ' + projectDetail.city + ' ' + projectDetail.district + ' ' + projectDetail.estate_name}}</span>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="control-label">状态: </label>
                            <span>{{projectDetail.project_state_text}}</span>
                        </div>

                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">工程项目详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="请输入空间" id="searchText"/>
                                </div>
                                <div class="form-group">
                                    <button  class="btn btn-info" v-on:click="searchClick">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right" style="margin-right: 5px">
                                <button id="addM" class="btn btn-info" v-on:click="submitClick" v-if="projectDetail.project_state === '1' || projectDetail.project_state === '4'">
                                   提交
                                </button>
                            </div>
                            <div class="form-group pull-right" style="margin-right: 20px">
                                <button class="btn btn-info" v-on:click="downloadClick">
                                    下载模版
                                </button>
                            </div>
                            <div class="form-group pull-right" style="margin-right: 20px">
                                <button id="" class="btn btn-info" v-on:click="exportClick" v-if="projectDetail.project_state === '1' || projectDetail.project_state === '4'">
                                    导入
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectDetailTable"></table>
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-left: 20px">
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">合计</span>
                                <div>
                                    <input class="form-control" id="total_price" v-model="calculate_price" disabled>
                                </div>
                            </div>
                        </div>
                        <!--<div class="col-sm-2">-->
                            <!--<button class="btn btn-primary" v-on:click="calculateClick">计算</button>-->
                        <!--</div>-->
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">最终报价</span>
                                <div>
                                    <input class="form-control" id="over_price" v-model="projectDetail.project_quoted_amount" v-bind:disabled="projectDetail != null && projectDetail.project_state == '2'">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="ImportSpaceModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">导入空间</h4>
                    </div>

                    <div class="modal-body">
                        <div class="form-group col-md-12">
                            <label>选择文件：</label><span>（请选.csv文件）</span>
                            <div>
                                <input type="file" id="spaceFile" class="form-control input-sm" accept=".csv" size="1">
                                <span class="margin-top-ten">{{filePath}}</span>
                            </div>
                        </div>

                    </div>
                    <div class="modal-footer" style="border-top:none">
                        <button type="button" class="btn btn-info" id="importAct" v-on:click="importFile">导入</button>
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
                projectId: '',
                projectDetail: {},
                oldRow:{},
                calculate_price:null,
                over_price:null,
                userinfo: common.getStoreData('userinfo'),
                fileUrl: null,
                filePath: null
            }
        },
        name: 'NCAProjectControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectDetailTable');
            _self.projectId = _self.$route.query.projectId


            function queryParams(params) {
                if ($('#searchText').val){
                    params.project_id = _self.projectId
                    params.search_text = $('#searchText').val()
                }
                return JSON.stringify(params);
            }

            function operateFormatter(value, row, index) {
                if (_self.projectDetail.project_state === '1' || _self.projectDetail.project_state === '4') {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 tableDetail">查看</a>' +
                        '<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>',
                    ].join('')
                } else {
                    return [
                        '<a class="btn btn-info btn-xs m-r-5 tableDetail">查看</a>'
                    ].join('')
                }
            }

            window.tableEvents = {
                'click .tableDetail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAProjectSpaceDetailControl',
                        query: {
                            projectId: _self.projectId,
                            projectDetailId: row.project_detail_id
                        }
                    })
                },
                'click .tableDelete': function(e, value, row, index) {
                    let msg =  '您确定删除'+ row.space_name +'么？'
                    common.rowDeleteWithApi(_self, msg, apiUrl + 'delete_detail', $table, row, 'project_detail_id', function(){
                        $('#projectDetailTable').bootstrapTable("refresh")
                    })
                }
            };

            function initTable() {
                let columns = [];
                if (_self.projectDetail.project_state === '1' || _self.projectDetail.project_state === '4') {
                    columns = [
                        common.BTRowFormat('project_detail_id','项目预算详情ID'),
                        common.BTRowFormatAlignLeft('roomtype_name', '户型名称'),
                        common.BTRowFormatAlignLeft('space_name', '空间名称'),
                        /*common.BTRowFormat('space_budget_amount', '空间预算金额'),*/
                        common.BTRowFormatWithFormatterAlignRight('space_budget_amount', '空间预算金额',common.priceFormat),
                        common.BTRowFormatEnumberWMinALginRight('space_count', '空间数量'),
                        /*common.BTRowFormat('space_total_amount', '总金额'),*/
                        common.BTRowFormatWithFormatterAlignRight('space_total_amount', '总金额',common.priceFormat),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ]
                } else {
                    columns = [
                        common.BTRowFormat('project_detail_id','项目预算详情ID'),
                        common.BTRowFormatAlignLeft('roomtype_name', '户型名称'),
                        common.BTRowFormatAlignLeft('space_name', '空间名称'),
                        common.BTRowFormatWithFormatterAlignRight('space_budget_amount', '空间预算金额',common.priceFormat),
                        common.BTRowFormatEnumberWMinALginRight('space_count', '空间数量'),
                        common.BTRowFormatWithFormatterAlignRight('space_total_amount', '总金额',common.priceFormat),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ]
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_detail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        _self.calculate_price = res.info.total_price
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    sortOrder:'asc',
                    idField: 'project_detail_id',
                    uniqueId: 'project_detail_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify_detail', row, 'project_detail_id', $table)
                    }
                });
                common.changeTableClass($table)
            }

            async function getInit() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info
                    let project = await _self.$http.post(apiUrl + 'search', {project_id: _self.projectId});
                    _self.projectDetail = project.data.info.rows[0];
                    for (let s of _self.pagePara.projectState) {
                        //已经是决算单，预算单的状态全部为已通过
                        if (_self.projectDetail.project_state === '3' || _self.projectDetail.project_state === '5' || _self.projectDetail.project_state === '6' || _self.projectDetail.project_state === '7') {
                            _self.projectDetail.project_state_text = '已通过'
                            break;
                        }
                        if (s.id === _self.projectDetail.project_state) {
                            _self.projectDetail.project_state_text = s.text;
                        }
                    }
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            async function initPage() {
                await getInit()
                initTable()
                _self.initFormData();
            }

            $(async function() {
                await initPage();
            });
        },
        methods: {
            submitClick: function () {
                let _self = this
                if (_self.projectDetail.project_quoted_amount === null) {
                    return common.dealWarningCommon("请输入最终价格！");
                }

                let param = {
                    project_id: _self.projectId,
                    project_quoted_amount: parseInt(_self.projectDetail.project_quoted_amount)
                }
                _self.$http.post(apiUrl + 'submit_detail', param).then((response) => {
                    let retData = response.data
                    if (retData.errno === 0) {
//                        _self.projectDetail.project_state = retData.info.project_state
//                        for (let s of _self.pagePara.projectState) {
//                            if (s.id === _self.projectDetail.project_state) {
//                                _self.projectDetail.project_state_text = s.text;
//                            }
//                        }
                        common.dealSuccessCommon('提交成功')
                        window.history.back()
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
            },
            initFormData: function () {
                let _self = this;
                $('#spaceFile').change(function() {
                    let files = this.files;
                    if (files.length > 0) {
                        let formData = new FormData();
                        formData.append('file', files[0]);
                        _self.filePath = $('#file').val();
                        _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                            if (response.data.errno === 0) {
                                _self.fileUrl = response.data.info.url;
                            } else {
                                common.dealErrorCommon(_self, response)
                            }
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }
                });
            },
            exportClick: function () {
                $('#ImportSpaceModal').modal('show')
                $('#spaceFile').val('');
            },
            downloadClick:function () {
                let _self = this;
                _self.$http.post(apiUrl + 'download_template', {}).then((response) => {
                    let downUrl = response.data.info;
                    window.location.href = window.location.protocol + '//' + window.location.host + downUrl;
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            importFile: function () {
                let _self = this
                if (!_self.fileUrl) {
                    return common.dealWarningCommon('请选择文件')
                }

                let params = {
                    uploadurl: _self.fileUrl,
                    project_id: _self.projectId
                }
                this.$http.post(apiUrl + 'import_space', params).then((response) => {
                    $('#projectDetailTable').bootstrapTable("refresh")
                    common.dealSuccessCommon('保存成功');
                }, (response) => {
                    common.dealErrorCommon(this, response)
                });

                $('#ImportSpaceModal').modal('hide')
            },
            calculateClick: function () {
                let _self = this
                _self.$http.post(apiUrl + 'calculate_detail', {project_id: _self.projectId}).then((response) => {
                    let retData = response.data
                    if (retData.errno === 0) {
                        _self.calculate_price = retData.info.total_price
                        common.dealSuccessCommon('计算成功')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
            },
            searchClick: function() {
                let _self = this
                $('#projectDetailTable').bootstrapTable("refresh")
            }
        }
    }
</script>
<style scoped>
</style>
