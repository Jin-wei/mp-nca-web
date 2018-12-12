<template>
    <div>
        <!--&lt;!&ndash; begin breadcrumb &ndash;&gt;-->
        <!--<ol class="breadcrumb">-->
        <!--&lt;!&ndash;<li><a href="javascript:">事务管理</a></li>&ndash;&gt;-->
        <!--<li class="active">工程项目管理</li>-->
        <!--</ol>-->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse" style="margin-top: 45px;">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">项目信息</h4>
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
                        <h4 class="panel-title">空间预算详情</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="select_space_position" @change="changePositionType" style="width: 150px">
                                    <option value="">请选择空间位置</option>
                                    <option v-for="type in pagePara.spacePositionInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <select class="select2 form-control select-width" id="worker_type" @change="changeWorkerType" style="width: 150px">
                                    <option value="">请选择工种类型</option>
                                    <option v-for="type in pagePara.professionInfo" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addClick" v-if="projectDetail.project_state === '1' || projectDetail.project_state === '4'">
                                    新增
                                </button>
                            </div>
                            <!--<div class="form-group pull-right" style="margin-right: 20px">-->
                                <!--<button id="saveM" class="btn btn-info" v-on:click="saveClick" v-if="projectDetail.project_state === '1' || projectDetail.project_state === '4'">-->
                                    <!--保存-->
                                <!--</button>-->
                            <!--</div>-->
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectSpaceDetailTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="createTaskModal" data-backdrop="static">
            <div class="modal-dialog" style="width: 700px">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增</h4>
                    </div>
                    <form @submit.prevent="submitProject" id="formProject">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">空间位置</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple id="space_position" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">施工项</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="space_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">数量</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="space_count" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">材料预算单价</label>
                                <div class="col-sm-8">
                                    <input class="form-control" id="material_budget" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">工种</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="space_worker_type" data-parsley-required="true"></select>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info"><i class="fa fa-fw fa-plus"></i>增加</button>
                        </div>
                    </form>
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
                projectDetailId: '',
                projectDetail: {},
                oldRow:{},
                userinfo: common.getStoreData('userinfo')
            }
        },
        name: 'NCAProjectControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectSpaceDetailTable');
            _self.projectId = _self.$route.query.projectId
            _self.projectDetailId = _self.$route.query.projectDetailId


            function queryParams(params) {
                params.project_detail_id = _self.projectDetailId
                if ($('#select_space_position').val()){
                    params.project_space_position = $('#select_space_position').val()
                }
                if ($('#worker_type').val()){
                    params.worker_id = $('#worker_type').val()
                }
                return JSON.stringify(params);
            }

            function workerFormatter(value, row, index) {
                for (let w of _self.pagePara.professionInfo) {
                    if (w.id == value) {
                        return w.text;
                    }
                }
            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>',
                ].join('')
            }

            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    let msg =  '您确定删除'+ row.project_space_name +'么？'
                    common.rowDeleteWithApi(_self, msg, apiUrl + 'delete_space', $table, row, 'project_space_id', function(){})
                }
            };

            function initTable() {
                let columns = [];
                if (_self.projectDetail.project_state === '1' || _self.projectDetail.project_state === '4') {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEdSelectAlignLeft(_self, 'project_space_position', '空间位置', 'spacePositionInfo'),
                        common.BTRowFormatEditableLeft('project_space_name', '施工项'),
                        common.BTRowFormatEdSelectAlignLeft(_self, 'worker_id', '工种', 'professionInfo'),
                        common.BTRowFormatEnumberWMinALginRight('count', '数量'),
                        common.BTRowFormat('worker_unit', '计量单位'),
                        common.BTRowFormatEnumberWMinALginRight('worker_budget', '人工预算单价',common.priceFormat),
                        common.BTRowFormatEnumberWMinALginRight('material_budget', '材料预算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_budget', '人工预算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_budget', '材料预算总价',common.priceFormat),
                        common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)
                    ]
                } else {
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('project_space_position', '空间位置'),
                        common.BTRowFormatAlignLeft('project_space_name', '施工项'),
                        common.BTRowFormatWithFormatterAlignLeft('worker_id', '工种',workerFormatter),
                        common.BTRowFormatAlignRight('count', '数量'),
                        common.BTRowFormat('worker_unit', '计量单位'),
                        common.BTRowFormatWithFormatterAlignRight('worker_budget', '人工预算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_budget', '材料预算单价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('worker_total_budget', '人工预算总价',common.priceFormat),
                        common.BTRowFormatWithFormatterAlignRight('material_total_budget', '材料预算总价',common.priceFormat),
                    ]
                }

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_space_detail',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    sortOrder:'asc',
                    idField: 'project_space_id',
                    uniqueId: 'project_space_id',
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
                        common.rowModifyWithT(_self, apiUrl + 'modify_space', row, 'project_space_id', $table)
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
                common.initSelect2($('#space_worker_type'), _self.pagePara.professionInfo);
                common.initSelect2($('#space_position'), _self.pagePara.spacePositionInfo);
                $('#formProject').parsley()
            }

            $(async function() {
                await initPage();
            });
        },
        methods: {
            addClick: function () {
                $('#formProject').parsley().reset()
                $('#createTaskModal').modal('show')
            },
            saveClick: function () {
                let _self = this
                _self.$http.post(apiUrl + 'save_space', {project_detail_id: _self.projectDetailId}).then((response) => {
                    let retData = response.data
                    if (retData.errno === 0) {
                        common.dealSuccessCommon('保存成功')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                },(error) =>{
                    common.dealErrorCommon(_self, error)
                })
            },
            changeWorkerType: function () {
                let _self = this
                $('#projectSpaceDetailTable').bootstrapTable("refresh", _self.queryParams)
            },
            changePositionType: function() {
                let _self = this
                $('#projectSpaceDetailTable').bootstrapTable("refresh", _self.queryParams)
            },
            submitProject: function() {
                let _self = this
                if ($('#formProject').parsley().isValid()) {
                    let param = {
                        project_id: _self.projectId,
                        project_detail_id: _self.projectDetailId,
                        project_space_position: common.getSelect2Text('space_position'),
                        project_space_name: $('#space_name').val(),
                        count: $('#space_count').val(),
                        material_budget: $('#material_budget').val(),
                        worker_id: common.getSelect2Val('space_worker_type'),
                    }
                    _self.$http.post(apiUrl + 'add_space', param).then((response) => {
                        let retData = response.data
                        if (retData.errno === 0) {
                            common.dealSuccessCommon('项目创建成功')
                            $('#projectSpaceDetailTable').bootstrapTable("refresh", _self.queryParams)
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    },(error) =>{
                        common.dealErrorCommon(_self, error)
                    })
                    $('#createTaskModal').modal('hide')
                }
            },
        }
    }
</script>
<style scoped>
</style>
