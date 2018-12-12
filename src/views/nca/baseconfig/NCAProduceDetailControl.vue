<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back();">产品维护</a></li>
        <li class="active">产品维护详情</li>
    </ol>
    <!-- end breadcrumb -->

    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">产品详情</h4>
                </div>
                <div class="panel-toolbar">

                    <div class="form-inline" role="form" v-if="showIndex==1">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索工序名称" id="search_textP" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryProcess" class="btn btn-info" v-on:click="showTable(1)">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right" v-show="produce_domain_id==user_domain_id">
                            <button id="addProcess" class="btn btn-info" v-on:click="addProcess" >增加
                            </button>
                        </div>
                    </div>

                    <div class="form-inline" role="form" v-if="showIndex==2">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索物料名称,编码" id="search_textM" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryMateriel" class="btn btn-info" v-on:click="showTable(2)">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right" v-show="produce_domain_id==user_domain_id">
                            <button id="addMateriel" class="btn btn-info" v-on:click="addMateriel">增加
                            </button>
                        </div>
                    </div>

                    <div class="form-inline" role="form" v-if="showIndex==3">
                        <div class="form-group">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="搜索验收项目名称,编码" id="search_textA" style="width: 180px">
                            </div>
                            <div class="form-group">
                                <button id="queryAcceptance" class="btn btn-info" v-on:click="showTable(3)">
                                    <i class="fa fa-search"></i>
                                </button>
                            </div>
                        </div>
                        <div class="form-group pull-right" v-show="produce_domain_id==user_domain_id">
                            <button id="addAcceptance" class="btn btn-info" v-on:click="addAcceptance">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <ul class="nav nav-tabs">
                        <li class="active" @click="showTable(1)">
                            <a href="#process" data-toggle="tab">
                                <span>工序维护</span>
                            </a>
                        </li>
                        <li class="" @click="showTable(2)">
                            <a href="#materiel" data-toggle="tab">
                                <span>物料详情</span>
                            </a>
                        </li>
                        <li class="" @click="showTable(3)">
                            <a href="#acceptance" data-toggle="tab">
                                <span>验收项</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content">
                        <div class="tab-pane fade active in" id="process">
                            <table id="processTable"></table>
                        </div>
                        <div class="tab-pane fade" id="materiel" >
                            <table id="materielTable"></table>
                        </div>
                        <div class="tab-pane fade" id="acceptance" >
                            <table id="acceptanceTable"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>


    <div class="modal fade" id="AddModalProcess">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增工序记录</h4>
                </div>
                <form @submit.prevent="saveProcess" id="formA">
                    <div class="modal-body row" style="margin-left: 10px; margin-right: 10px">
                        <div class="row">
                            <div class="form-group col-xs-6">
                                <label>工序名称:</label>
                                <select class="form-control select2" multiple id="process_name" data-parsley-required="true"></select>
                            </div>
                            <div class="form-group col-xs-6">
                                <label>序号:</label>
                                <input class="form-control" v-model="workRow.process_level" data-parsley-type="number" data-parsley-required="true">
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label>时长:</label>
                                <input class="form-control" v-model="workRow.process_duration" data-parsley-type="number" data-parsley-required="true">
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <label>描述</label>
                                <textarea class="form-control" v-model="workRow.process_description" maxlength="200" data-parsley-maxlength="200"></textarea>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddMaterielModal">
        <div class="modal-dialog" style="width: 1200px">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增物料</h4>
                </div>
                <div class="modal-body">
                    <div class="form-group col-xs-10">
                        <input class="form-control" id="matNameOrCodeOrFormat" placeholder="搜索物料编码、名称、规格型号" v-on:input="searchMate">
                    </div>
                    <div class="form-group col-xs-2 no-padding" style="text-align: right">
                        <button type="button" class="btn btn-info" v-on:click="saveMateriel">保存</button>
                    </div>
                    <table id="tableSearch"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddAcceptanceModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 v-if="!workRow.produceacceptance_id" class="modal-title">新建验收项</h4>
                    <h4 v-else class="modal-title">编辑验收项</h4>
                </div>
                <form @submit.prevent="submitForm" id="formB">
                    <div class="modal-body row">
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">验收编号</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.acceptance_index" data-parsley-required="true" data-parsley-maxlength="11" data-parsley-type="integer">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">验收项</label>
                            <div class="col-sm-8">
                                <input class="form-control" v-model="workRow.acceptance_name" data-parsley-required="true" data-parsley-maxlength="50">
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">施工工序</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" id="process_id" data-parsley-required="true"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">空间</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" id="room_type" data-parsley-required="true"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">是否隐藏</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" id="is_hidden" data-parsley-required="true"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-6">
                            <label class="col-sm-4 control-label">上传格式</label>
                            <div class="col-sm-8">
                                <select class="form-control select2" id="upload_format" data-parsley-required="true"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <label class="col-sm-2 control-label">采集证据要求</label>
                            <div class="col-sm-10">
                                <textarea class="form-control" v-model="workRow.evidence_require" maxlength="500" data-parsley-required="true" data-parsley-maxlength="500"></textarea>
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <label class="col-sm-2 control-label">工艺要求</label>
                            <div class="col-sm-10">
                                <textarea class="form-control" v-model="workRow.technological_require" maxlength="500" data-parsley-required="true" data-parsley-maxlength="500"></textarea>
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <div style="margin-left: 30px">
                                <label class="btn btn-info btn-xs fileupload-button">上传实例
                                    <input id='acceptanceUpload' type="file" name="file">
                                </label>
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <div class="image-div">
                                <div class="image-content" v-for="(url, index) in uploadImages">
                                    <img class="image-display" :src="url">
                                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-danger delete-btn" @click="removImage(index)">
                                        <i class="fa fa-times"></i>
                                    </a>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" v-if="workRow.produceacceptance_id" class="btn btn-info">修改</button>
                        <button type="submit" v-else="" class="btn btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>

</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAProduceControl?method=';

export default {
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            workRow: {},
            oldRow:{},
            produce_id:'',
            showIndex:1,
            searchedRow:[],
            uploadImages: [], //记录上传图片的url,用于显示
            files: [], //记录上传图片的file信息，Object类型
            produce_domain_id:'',
            user_domain_id:''
        }
    },
    name: 'NCAProduceDetailControl',
    methods: {
        getPagePara: async function(event) {
            let _self = this;
            try {
                _self.produce_id=common.getUrlParams('produceId');
                _self.produce_domain_id=common.getUrlParams('produceDomainId');
                _self.user_domain_id=_self.userinfo.domain_id;
                let response = await _self.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                _self.pagePara = JSON.parse(JSON.stringify(retData));

                common.initSelect2($('#process_name'), _self.pagePara.materielProcedure);
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        showTable:async function(index){
            let _self = this;
            _self.showIndex = index;
            let response = await _self.$http.post(apiUrl + 'getProcess', {produce_id:_self.produce_id});
            let retData = response.data.info;
            _self.pagePara.produceProcess = retData.produceProcess;

            common.initSelect2($('#process_id'), _self.pagePara.produceProcess);
            if(index==1){
                $('#processTable').bootstrapTable("refresh");
            }else if (index==2){

                $('#materielTable').bootstrapTable('destroy');
                await _self.initTableProduceMateriel()
            }else if (index==3){
                $('#acceptanceTable').bootstrapTable("refresh");
            }
        },

        addProcess: function(event) {
            let _self = this;
            _self.workRow={};
            $('#formA').parsley().reset();
            $('#process_name').val(null).trigger('change');
            $('#AddModalProcess').modal('show')
        },
        saveProcess:async function(event){
            let _self = this;
            try {
                if ($('#formA').parsley().isValid()) {
                    _self.workRow.produce_id = _self.produce_id;
                    _self.workRow.process_name = common.getSelect2Val('process_name')
                    let response = await _self.$http.post(apiUrl + 'addProcess', _self.workRow);
                    common.dealSuccessCommon('增加成功');
                    $('#processTable').bootstrapTable("refresh");
                    $('#AddModalProcess').modal('hide')
                }
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },

        addMateriel: async function(event) {
            let _self = this;
            await _self.initTableMateriel();
            $('#AddMaterielModal').modal('show')
        },
        saveMateriel:async function(event){
            let _self = this;
            let params={
                searchedRow:_self.searchedRow,
                produce_id:_self.produce_id
            };

            let response = await _self.$http.post(apiUrl + 'addProcessMateriel', params);
            $('#materielTable').bootstrapTable("refresh");
            $('#AddMaterielModal').modal('hide');
        },
        searchMate: function(event) {
            let _self = this;
            _self.searchedRow = [];
            $('#tableSearch').bootstrapTable("refresh")
        },

        addAcceptance:async function(event){
            let _self = this;
            _self.workRow={};
            $('#formB').parsley().reset();
            $('#AddAcceptanceModal').modal('show')
        },
        queryParamsMateriel:function(params) {
            let _self = this;
            params.matNameOrCodeOrFormat = $('#matNameOrCodeOrFormat').val();
            params.produce_id=_self.produce_id;
            params.tableName='purduceDetail';
            return JSON.stringify(params)
        },
        initTableMateriel:function(event){
            let _self = this;
            let $tableSearch = $('#tableSearch');
            $tableSearch.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getMateriel',
                queryParams: _self.queryParamsMateriel,
                sidePagination: 'server',
                ajaxOptions:common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    {
                        field: 'state',
                        checkbox: true
                    },
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '物料编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '物料名称'),
                    common.BTRowFormat('materiel_format', '规格型号'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    /*common.BTRowFormat('materiel_cost', '采购价'),
                    common.BTRowFormat('materiel_sale', '销售价'),
*/
                    common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormat('domain_name', '所属机构')
                ],
                idField: 'materiel_id',
                uniqueId: 'materiel_id',
                clickToSelect: true,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 25,50,100],
                locale: 'zh-CN',
                onCheckAll: function(rows) {
                    for (let i = 0; i < rows.length; i++) {
                        _self.searchedRow.push(rows[i])
                    }
                },
                onUncheckAll: function(rows) {
                    _self.searchedRow = []
                },
                onCheck: function(row, $element) {
                    _self.searchedRow.push(row)
                },
                onUncheck: function(row, $element) {
                    _self.searchedRow.splice($.inArray(row, _self.searchedRow), 1);
                },
                onPageChange: function(number, size) {
                    _self.searchedRow = []
                }
            });
            common.changeTableClass($tableSearch)
        },

        queryParamsProduceMateriel:function(params) {
            let _self = this;
            params.produce_id=_self.produce_id;
            params.search_textM = $('#search_textM').val();
            return JSON.stringify(params)
        },
        initTableProduceMateriel:function(event){
            let _self = this;
            let $materielTable = $('#materielTable');
            window.tableEvents = {
                'click .delete_materiel': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '产品物料', apiUrl + 'deleteProcessMateriel', $materielTable, row, 'producemateriel_id', function(){})
                }
            };
            let colunms=[];
            if(_self.produce_domain_id==_self.user_domain_id){
                colunms=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '产品编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格编码'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价',common.priceFormat),
                    common.BTRowFormatEdSelect2(_self, 'produceprocess_id', '投料工序', 'produceProcess'),
                    common.actFormatter('act', ()=>{
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 delete_materiel">删除</a>'
                        ].join('')
                    }, tableEvents)
                ]
            }else {
                colunms=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('materiel_code', '产品编码'),
                    common.BTRowFormatAlignLeft('materiel_name', '产品名称'),
                    common.BTRowFormat('materiel_format', '规格编码'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_type', '物料分类', 'materielType'),
                    common.BTRowFormatEdSelect2Disabled(_self, 'materiel_unit', '单位', 'unitInfo'),
                    common.BTRowFormatWithFormatterAlignRight('materiel_cost', '采购价',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('materiel_sale', '销售价',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 'produceprocess_id', '投料工序', 'produceProcess')
                ]
            }
            $materielTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProcessMateriel',
                queryParams: _self.queryParamsProduceMateriel,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: colunms,
                idField: 'producemateriel_id',
                uniqueId: 'producemateriel_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modifyProcessMateriel', row, 'producemateriel_id', $materielTable)
                }
            });
            common.changeTableClass($materielTable)
        },

        queryParamsProcess:function(params) {
            let _self = this;
            params.produce_id=_self.produce_id;
            params.search_textP = $('#search_textP').val();
            return JSON.stringify(params)
        },
        initTableProcess:function(event){
            let _self = this;
            let $processTable = $('#processTable');
            window.tableEvents = {
                'click .delete_process': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '产品工序', apiUrl + 'deleteProcess', $processTable, row, 'produceprocess_id', function(){})
                }
            };
            let columns=[];
            if(_self.produce_domain_id==_self.user_domain_id){
                columns=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('process_name', '工序名称'),
                    common.BTRowFormatEnumberWMin('process_level', '序号'),
                    common.BTRowFormatEnumberWMinALginRight('process_duration', '时长'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('process_description', '工序描述',150 ,10),
                    common.actFormatter('act', ()=>{
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 delete_process">删除</a>'
                        ].join('')
                    }, tableEvents)
                ]
            }else{
                columns=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('process_name', '工序名称'),
                    common.BTRowFormat('process_level', '序号'),
                    common.BTRowFormatAlignRight('process_duration', '时长'),
                    common.BTRowFormatWithFormatterAlignLeft('process_description', '工序描述',common.remarkFormatter)
                ]
            }
            $processTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'searchProcess',
                queryParams: _self.queryParamsProcess,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: columns,
                idField: 'produceprocess_id',
                uniqueId: 'produceprocess_id',
                striped: true,
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize:common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modifyProcess', row, 'produceprocess_id', $processTable)
                }
            });
            common.changeTableClass($processTable)
        },

        queryParamsAcceptance:function(params){
            let _self = this;
            common.initImageViewer();
            params.produce_id=_self.produce_id;
            params.search_textA = $('#search_textA').val();
            return JSON.stringify(params);
        },
        initTableAcceptance:function(event){
            let _self = this;
            let $acceptanceTable = $('#acceptanceTable');

            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    _self.rowDelete(_self, '删除', apiUrl, row, 'produceacceptance_id')
                },
                'click .tableEdit': function(e, value, row, index) {
                    _self.oldRow = $.extend(true, {}, row);
                    _self.workRow = $.extend(true, {}, row);
                    $('#process_id').val(_self.workRow.produceprocess_id).trigger('change');
                    $('#room_type').val(_self.workRow.room_type).trigger('change');
                    $('#is_hidden').val(_self.workRow.is_hidden).trigger('change');
                    $('#upload_format').val(_self.workRow.upload_format).trigger('change');
                    _self.uploadImages = []
                    for (let i = 0; i < _self.workRow.images.length; i++) {
                        let image = _self.workRow.images[i];
                        _self.uploadImages.push(image.file_url)
                    }
                    _self.files = _self.workRow.images;
                    $('#AddAcceptanceModal').modal('show')
                }
            };
            let colunms=[];
            if(_self.produce_domain_id==_self.user_domain_id){
                colunms=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('acceptance_index', '验收编号'),
                    common.BTRowFormatAlignLeft('process_name', '施工工序'),
                    common.BTRowFormatWithFormatter('room_type', '空间', _self.roomTypeFilter),
                    common.BTRowFormatAlignLeft('acceptance_name', '验收项'),
                    common.BTRowFormatWithFormatter('is_hidden', '隐蔽', _self.trueFalseTypeFilter),
                    common.BTRowFormatWithFormatterAlignLeft('technological_require', '工艺要求',common.remarkFormatter),
                    common.BTRowFormatWithFormatterAlignLeft('evidence_require', '采集证据要求',common.remarkFormatter),
                    common.BTRowFormatWithFormatter('upload_format', '上传格式', _self.acceptanceTypeFilter),
                    common.BTRowFormatWithFormatter('images', '上传实例', _self.acceptanceFormatter),
                    common.actFormatter('edit', ()=>{
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 tableEdit btn-primary">编辑</a>' +
                            '<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>'
                        ].join('')
                    }, tableEvents)
                ]
            }else {
                colunms=[
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('acceptance_index', '验收编号'),
                    common.BTRowFormatAlignLeft('process_name', '施工工序'),
                    common.BTRowFormatWithFormatter('room_type', '空间', _self.roomTypeFilter),
                    common.BTRowFormatAlignLeft('acceptance_name', '验收项'),
                    common.BTRowFormatWithFormatter('is_hidden', '隐蔽', _self.trueFalseTypeFilter),
                    common.BTRowFormatWithFormatterAlignLeft('technological_require', '工艺要求',common.remarkFormatter),
                    common.BTRowFormatWithFormatterAlignLeft('evidence_require', '采集证据要求',common.remarkFormatter),
                    common.BTRowFormatWithFormatter('upload_format', '上传格式', _self.acceptanceTypeFilter),
                    common.BTRowFormatWithFormatter('images', '上传实例', _self.acceptanceFormatter)
                ]
            }
            $acceptanceTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search_acceptance',
                queryParams: _self.queryParamsAcceptance,
                sidePagination: 'server',
                ajaxOptions:common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: colunms,
                sortOrder: 'asc',
                idField: 'produceacceptance_id',
                uniqueId: 'produceacceptance_id',
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                striped: true,
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function () {
                        $(this).popover()
                    })
                }
            });
            common.changeTableClass($acceptanceTable)
        },

        roomTypeFilter:function(value){
            let _self = this;
            for (let i = 0; i < _self.pagePara.roomTypeInfo.length; i++) {
                if (_self.pagePara.roomTypeInfo[i].id === value) {
                    return _self.pagePara.roomTypeInfo[i].text
                }
            }
            return ''
        },
        trueFalseTypeFilter:function(value){
            let _self = this;
            for (let i = 0; i < _self.pagePara.trueFalseInfo.length; i++) {
                if (_self.pagePara.trueFalseInfo[i].id === value) {
                    return _self.pagePara.trueFalseInfo[i].text
                }
            }
            return ''
        },
        acceptanceTypeFilter:function(value){
            let _self = this;
            for (let i = 0; i < _self.pagePara.acceptanceInfo.length; i++) {
                if (_self.pagePara.acceptanceInfo[i].id === value) {
                    return _self.pagePara.acceptanceInfo[i].text
                }
            }
            return ''
        },
        acceptanceFormatter:function(value, row, index){
            let _self = this;
            let images = row.images
            let retString = '<div class="form-inline image" role="form">'
            for (let i = 0; i < images.length; i++) {
                let url = images[i].file_url
                retString += '<sapn><img class="icon-size" src="' + url + '"></sapn>'
            }
            retString += '</div>'
            return retString
        },
        rowDelete:function(_self, msg, acceptApiUrl, row, key){
            common.dealConfrimCommon(msg, function() {
                _self.$http.post(acceptApiUrl + 'delete_acceptance', row).then((response) => {
                    $('#acceptanceTable').bootstrapTable('remove', {
                        field: key,
                        values: [row[key]]
                    });
                    common.dealSuccessCommon('删除成功');
                    console.log('delete success');
                }, (response) => {
                    console.log('delete error');
                    common.dealErrorCommon(_self, response);
                });
            });
        },

        removImage: async function(index) {
            let _self = this;
            _self.uploadImages.splice(index, 1);
            _self.files.splice(index, 1)
        },
        submitForm: async function(event) {
            let _self = this;
            let formB = $('#formB')
            if (formB.parsley().isValid()) {
                _self.workRow.produce_id = _self.produce_id;
                _self.workRow.produceprocess_id = common.getSelect2Val('process_id');
                _self.workRow.room_type = common.getSelect2Val('room_type');
                _self.workRow.is_hidden = common.getSelect2Val('is_hidden');
                _self.workRow.upload_format = common.getSelect2Val('upload_format');
                _self.workRow.images = _self.files;
                try {
                    let apiName = _self.workRow.produceacceptance_id ? 'modify_acceptance' : 'add_acceptance';
                    let param = _self.workRow.produceacceptance_id ? {
                        'old': _self.oldRow,
                        'new': _self.workRow
                    } : _self.workRow;
                    let response = await _self.$http.post(apiUrl + apiName, param);
                    let retData = response.data.info;
                    $('#acceptanceTable').bootstrapTable("refresh");
                    formB.parsley().reset();
                    $('#AddAcceptanceModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        }
    },
    mounted: async function() {
        let _self = this;

        await _self.getPagePara();
        await _self.initTableProcess();
        await _self.initTableAcceptance();


        common.initSelect2($('#room_type'), _self.pagePara.roomTypeInfo);
        common.initSelect2($('#is_hidden'), _self.pagePara.trueFalseInfo);
        common.initSelect2($('#upload_format'), _self.pagePara.acceptanceInfo);

        common.fileUpload(_self, $('#acceptanceUpload'), apiUrl, function(fileInfo) {
            _self.uploadImages.push(fileInfo.url)
            let convert = {} //转换字段名
            convert.file_name = fileInfo.name
            convert.file_url = fileInfo.url
            convert.file_type = fileInfo.type
            _self.files.push(convert)
        }, (response) => {
            common.dealErrorCommon(_self, response)
        })

        common.reSizeCall();
        $('#formA').parsley();
    }
}
</script>
<style scoped>
    .p_r{
        position:relative;
    }
    .p_a{
        position:absolute;
        top:21px;
        right:10px;
    }
    .m_t{
        margin-top:-24px;
    }
    .image-div {
        margin-left: 20px
    }

    .image-content {
        width: 60px;
        position: relative;
        float: left;
    }

    .image-display {
        border: 1px solid #0fd16f;
        width: 50px;
        height: 50px;
        margin-right: 10px;
        margin-bottom: 10px;
    }

    .delete-btn {
        position: absolute;
        top: -7px;
        right: 2px;
    }
</style>
