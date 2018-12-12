<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">低值易耗品管理</a></li>
            <li class="active">{{_self.title}}</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">{{title}} {{consumablesParentCode}}</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <div class="form-group">
                                    <input class="form-control" id="search_text" :placeholder="searchPlaceholder"
                                           style="width: 270px;">
                                </div>
                                <div class="form-group">
                                    <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right" v-show="consumablesStatus==='0' || consumablesStatus==='2'">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                                <button class="btn btn-info" v-on:click="setTask">
                                    提交
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="default-tab">
                                <table id="table"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">{{modalTitle}}</h4>
                    </div>
                    <form @submit.prevent="submitData" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label"><span
                                            class="table-required">*</span>低值易耗品名称</label>
                                    <div class="col-sm-7">
                                        <input class="form-control" v-model="postData.consumables_name"
                                               maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"  type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label"><span
                                            class="table-required">*</span>规格型号</label>
                                    <div class="col-sm-7">
                                        <input  class="form-control" v-model="postData.consumables_specifications"
                                                maxlength="100" data-parsley-maxlength="100"  data-parsley-required="true"  type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6">
                                    <label class="col-sm-5 control-label"><span
                                            class="table-required">*</span>计量单位</label>
                                    <div class="col-sm-7">
                                        <input  class="form-control" v-model="postData.consumables_unit"
                                                maxlength="100" data-parsley-maxlength="100" data-parsley-required="true"  type="text">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6" v-if="consumablesTypeId==1">
                                    <label class="col-sm-5 control-label"><span
                                            class="table-required">*</span>数量</label>
                                    <div class="col-sm-7">
                                        <input  class="form-control" min="0" v-model="postData.consumables_number"
                                                maxlength="100" data-parsley-maxlength="100"  data-parsley-required="true"  type="number">
                                    </div>
                                </div>

                                <div class="form-group col-sm-6" v-if="consumablesTypeId==2">
                                    <label class="col-sm-5 control-label"><span
                                            class="table-required">*</span>验收类型</label>
                                    <div class="col-sm-7">
                                        <select class="form-control select2" id="acceptance_type"  data-parsley-required="true" ></select>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12"  v-if="consumablesTypeId==2">
                                    <label class="col-sm-2 control-label" style="margin-left: 19px;"><span
                                            class="table-required">*</span>管理责任人</label>
                                    <div class="col-sm-8">
                                        <input  class="form-control" id="administrator" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="choiceGroup()">选择</label>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12"  v-if="consumablesTypeId==2">
                                    <label class="col-sm-2 control-label" style="margin-left: 18px;"><span
                                            class="table-required">*</span>归属部门</label>
                                    <div class="col-sm-8">
                                        <input  class="form-control" id="department" data-parsley-required="true" disabled>
                                    </div>
                                    <div class="col-sm-1 padding-top-7">
                                        <label class="btn btn-info btn-xs fileupload-button" v-on:click="choiceDepartment()">选择</label>
                                    </div>
                                </div>

                                <div class="form-group col-sm-12" v-show="consumablesTypeId==2">
                                    <label class="col-sm-2 control-label" style="margin-left: 17px;"><span
                                            class="table-required"></span>实物照片</label>
                                    <div class="col-sm-9">
                                        <input id="file" type="file" style="margin-top: 10px" >
                                        <div  v-for="(file,index) in postData.files" class="col-sm-3 img-div" >
                                            <a :href=file.file_url target="_blank">
                                                <img class="consumables-img" :src="file.file_url" alt="">
                                            </a>
                                            <i class="glyphicon glyphicon-remove-sign delete_icon" v-on:click="removeImage(index,file)" ></i>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-primary btn-info">保存
                            </button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
        <userNewDepartment :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userNewDepartment>
        <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>
    </div>
</template>
<script>
    import Vue from 'vue'
    const common = require('commonFunc')
    const apiUrl = '/api/nca/longtermassets/NCAConsumablesDetailControlSRV?method='
    import userNewDepartment from '../baseconfig/userNewDepartment.vue';
    import userSelectDialog from '../../../components/common/userSelectDialog.vue'

    export default {
        data: function () {
            return {
                pagePara: {},
                oldRow: '',
                selectState: 0,
                postData: {
                    files:[]
                },
                title:"",
                consumablesStatus:'',
                consumablesParentCode:common.getUrlParams("consumablesCode"),
                consumablesTypeId:'',
                searchPlaceholder:'',
                isEdit:false,
                modalTitle:'增加',
                usergroupId: '',
                users: [],
                eventHub: new Vue(),
                administrator_id:'',
                show_return: 0,
                department_id:''
            }
        },
        name: 'NCAConsumablesDetailControlSRV',
        components: {
            userNewDepartment,
            userSelectDialog
        },
        mounted: function () {
            let _self = this;

            async function initData() {
                await _self.initData();
                _self.initTable();
                //title
                if(_self.consumablesTypeId==1){
                    _self.searchPlaceholder='低值易耗品名称';
                    _self.title='低值易耗品申购单';
                }else if(_self.consumablesTypeId==2){
                    _self.searchPlaceholder='搜索低值易耗品编号、低值易耗品名称';
                    _self.title='低值易耗品验收单';
                }else if(_self.consumablesTypeId==3){
                    _self.searchPlaceholder='搜索低值易耗品编号、低值易耗品名称';
                    _self.title='低值易耗品验收单';
                }
            }

            $(function () {
                initData();
                common.fileUpload(_self, $('#file'), apiUrl, function(fileInfo) {
                    let convert = {} //转换字段名
                    convert.file_name = fileInfo.name;
                    convert.file_url = fileInfo.url;
                    convert.file_type = fileInfo.type;
                    _self.postData.files.push(convert);
                    $('#file').val('');
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            })
        },
        methods: {
            initData: async function () {
                let _self = this;
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {consumables_code:_self.consumablesParentCode});
                    _self.pagePara = response.data.info;
                    console.log('_self.pagePara',_self.pagePara)
                    _self.consumablesTypeId = _self.pagePara.consumable.consumables_type_id;
                    _self.consumablesStatus = _self.pagePara.consumable.consumables_status;
                    _self.show_return = _self.$route.query.show_return;
                    console.log('show_return',_self.show_return)
                    window.setTimeout(function(){
                        common.initSelect2($('#acceptance_type'), _self.pagePara.LOW_VALUE_ACCEPTANCE_TYPE);
                    },100)
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            },
            initTable: function (){
                let $table = $('#table');
                let _self = this;
                window.tableEvents = {
                    "click .delete" :function (e, value, row, index){
                        common.rowDeleteWithApi(_self, '确认删除吗？', apiUrl + 'remove', $table, row, 'consumables_detail_id')
                    },
                    "click .edit" :function (e, value, row, index){
                        _self.isEdit=true;
                        _self.postData={};
                        _self.postData=row;
                        _self.modalTitle='修改';
                        _self.administrator_id=_self.postData.consumables_administrator_id;
                        $('#administrator').val(_self.postData.name)
                        $('#acceptance_type').val(_self.postData.consumables_acceptance_type_id).trigger('change');
                        $('#AddModal').modal('show')
                    },
                }

                let typeFormat = function (value,row) {
                     for(let i of _self.pagePara.LOW_VALUE_ACCEPTANCE_TYPE){
                         if(i.id===value){
                             return i.text
                         }
                     }
                }

                function queryParams(params) {
                    params.search_text = $('#search_text').val()
                    params.consumables_parent_code = _self.consumablesParentCode
                    console.log('_self.consumablesTypeId',_self.consumablesTypeId)
                    if (_self.consumablesTypeId == 3 ){
                        params.consumables_detail_type_id = 2
                    }else {
                        params.consumables_detail_type_id = _self.consumablesTypeId.toString()
                    }
                    return JSON.stringify(params)
                }

                function actFormatter(value, row) {
                    if (_self.consumablesTypeId==3) {
                        if (row.consumables_detail_status === '0' || row.consumables_detail_status === '2') {
                            return [
                                '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'
                            ].join('')
                        }
                    } else if (row.consumables_detail_status === '0' || row.consumables_detail_status === '2') {
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 delete">删除</a>'+
                            '<a class="btn btn-info btn-xs m-r-5 edit">修改</a>'
                        ].join('')
                    }
                }

                let columns;
                if(_self.consumablesTypeId==1){
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                        common.BTRowFormat('consumables_specifications', '规格型号'),
                        common.BTRowFormat('consumables_unit', '计量单位'),
                        common.BTRowFormatAlignRight('consumables_number', '数量'),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]

                }else if(_self.consumablesTypeId==2){
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                        common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                        common.BTRowFormat('consumables_specifications', '规格型号'),
                        common.BTRowFormat('consumables_unit', '计量单位'),
                        common.BTRowFormatWithFE('files', '实物照片', common.filesFormatter),
                        common.BTRowFormatAlignLeft('name', '管理责任人'),
                        common.BTRowFormatAlignLeft('department_name', '归属部门'),
                        common.BTRowFormatWithFormatter('consumables_acceptance_type_id', '验收类型',typeFormat),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                }else if(_self.consumablesTypeId==3){
                    columns = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormat('consumables_detail_code', '低值易耗品编号'),
                        common.BTRowFormatAlignLeft('consumables_name', '低值易耗品名称'),
                        common.BTRowFormat('consumables_specifications', '规格型号'),
                        common.BTRowFormat('consumables_unit', '计量单位'),
                        common.BTRowFormatAlignLeft('name', '管理责任人'),
                        common.BTRowFormatAlignLeft('department_name', '归属部门'),
                        common.BTRowFormatWithFormatter('consumables_acceptance_type_id', '验收类型',typeFormat),
                        common.actFormatter('act', actFormatter, tableEvents)
                    ]
                }

                $table.bootstrapTable('destroy');
                $table.bootstrapTable({
                    method: 'POST',
                    url:  apiUrl + 'search',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function (res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: columns,
                    idField: 'consumables_detail_id',
                    uniqueId: 'consumables_detail_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    undefinedText:'',
                    onPostBody: function () {
                    },
                    onEditableShown: function (field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function (field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'appoint_id', $table)
                    }
                })
                common.changeTableClass($table)
            },
            addM: function (event) {
                let _self = this;
                _self.postData={
                    files:[]
                };
                _self.administrator_id='';
                _self.isEdit=false;
                _self.modalTitle='新增低值易耗品记录';
                $('#administrator').val(null)
                $('#acceptance_type').val(null).trigger('change');
                $('#AddModal').modal('show')
            },
            submitData: function (event) {
                let _self = this;
                let url='';
                if($('#formA').parsley().isValid()){
                    if(_self.isEdit){
                        url= apiUrl + 'modify'
                        _self.postAct(url);
                    }else {
                        url= apiUrl + 'add'
                        _self.postAct(url);
                    }

                }
            },
            postAct:async function (url) {
                let _self = this;
                let params = $.extend(true,{},_self.postData);
                let acceptance_type= common.getSelect2Val('acceptance_type');
                params.consumables_detail_type_id=_self.consumablesTypeId.toString();
                params.consumables_parent_code=_self.consumablesParentCode.toString();
                params.consumables_administrator_id=_self.administrator_id;
                params.department_id=_self.department_id;
                params.consumables_acceptance_type_id=acceptance_type;
                params.files=[];
                if((params.consumables_acceptance_type_id==='' || params.consumables_acceptance_type_id===null) && _self.consumablesTypeId==2){
                    return  common.dealWarningCommon('请选择管理员！');
                }
                if((params.department_id==='' || params.department_id===null) && _self.consumablesTypeId==2){
                    return  common.dealWarningCommon('请选择部门！');
                }
                for(let i of _self.postData.files){
                    if(!i.file_id){
                        params.files.push(i);
                    }
                }

                _self.$http.post(url, params).then((response) => {
                    $('#AddModal').modal('hide');
                    common.dealSuccessCommon('保存成功！')
                    $('#formA').parsley().reset();

                    window.setTimeout(function () {
                        $('#table').bootstrapTable("refresh", _self.queryParams);
                    },10)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            search :function () {
                 let _self = this;
                 $('#table').bootstrapTable("refresh", _self.queryParams)
            },
            removeImage:function (index,file) {
                let _self=this;
                _self.postData.files.splice(index, 1);
                if(file.file_id){
                    var params={
                        file_id:file.file_id
                    }
                    _self.$http.post(apiUrl + 'removeFile', params).then(response =>{
                        if(response.body.errno==0){
                        }else {
                            console.log(response.msg)
                        }
                    },(response) =>{
                        common.dealErrorCommon(response);
                    })
                }
            },
            setTask:async function(){
                let _self = this;
                try {
                    let tableData=$('#table').bootstrapTable('getData');
                    if(tableData.length===0){
                        return  common.dealWarningCommon('请先增加数据！');
                    }
                    let response = await _self.$http.post(apiUrl + 'setTask', {consumables_code:_self.consumablesParentCode});
                    window.setTimeout(function(){
                        common.dealSuccessCommon('提交成功！')
                        _self.consumablesStatus='1';
                        $('#table').bootstrapTable("refresh", _self.queryParams);
                    },100)
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            },
            choiceGroup:function () {
                let _self = this;
                _self.eventHub.$emit('show-userselect-dialog');
            },
            choiceDepartment: function(index) {
                let _self = this;
                _self.eventHub.$emit('show-departmentselect-dialog');
            },
            departmentSelectCallback: async function(users) {
                let _self = this;
                try {
                    let currentUser = common.getStoreData('userinfo');
                    if(users.length==0){
                        return  common.dealWarningCommon('请选择部门！');
                    }
                    let params = {
                        user_id: currentUser.user_id,
                        users: users
                    }
                    let response =await _self.$http.post(apiUrl + 'changeDepartment' , params);
                    $("#department").val(response.data.info.userDepartmentName)
                    _self.department_id = response.data.info.userDepartmentId
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            },
            userSelectCallback : async function (users){
                let _self = this;
                try {
                    let currentUser = common.getStoreData('userinfo');
                    if(users.length==0){
                        return  common.dealWarningCommon('请选择管理员！');
                    }
                    let params = {
                        user_id: currentUser.user_id,
                        users: users
                    }
                    let response =await _self.$http.post(apiUrl + 'changeGroup' , params);
                    $("#administrator").val(response.data.info.userName)
                    _self.administrator_id = response.data.info.userId
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        }
    }
</script>
<style scoped>
    .select-width {
        width: 180px;
    }
    .consumables-img{
        width: 70px;
        height: 70px;
    }
    .img-div{
        margin-top: 10px;
        position: relative;
        font-size:16px
    }
    .delete_icon{
        position: absolute;
        right:0;
        top:-10px;
        z-index: 1;
        cursor: pointer;
    }
</style>
