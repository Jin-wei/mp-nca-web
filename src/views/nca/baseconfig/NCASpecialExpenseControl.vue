<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:">行政办公管理</a></li>
        <li class="active">资金支出列表</li>
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
                    <h4 class="panel-title">资金支出列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="search_text" placeholder="搜索申请单编号、申请人姓名" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <div class="form-group"  style="width: 150px">
                               <!-- <select class="select2 form-control select-width" id="type"></select>-->
                                <select class="select2 form-control select-width" id="type" style="width: 150px">
                                    <option value="">请选择类型</option>
                                    <option v-for="type in typeArr" v-bind:value="type.id">{{type.text}}</option>
                                </select>
                            </div>
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button v-if="sExpenseCode===null" id="addM" class="btn btn-info" v-on:click="showModal()">增加
                            </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="specialeExpenseTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="detailModal" data-backdrop="static">
        <div class="modal-dialog" style="width: 800px">
            <div class="modal-content" style="width: 800px">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title" v-if="isEdit==false">新增费用报销申请单</h4>
                    <h4 class="modal-title" v-if="isEdit==true">编辑费用报销申请单</h4>
                </div>
                <form @submit.prevent="postData" id="formB">
                    <div class="modal-body row" style="width: 799px">
                        <div class="col-md-12">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>费用报销事由分类</label>
                                <div class="col-sm-8">
                                    <select class="select2 form-control select-width" id="detailType" data-parsley-required="true">
                                        <option value="">请选择类型</option>
                                        <option v-for="type in typeArr" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                    <!--<input type="text" class="form-control" id="detailType"  data-parsley-required="true"/>-->
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>合计金额</label>
                                <div class="col-sm-8">
                                    <input class="form-control" min="1" step="1" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')"
                                           maxlength="50" data-parsley-maxlength="50"  data-parsley-required="true" id="s_sum_fee" disabled>
                                </div>
                            </div>
                        </div>

                        <div class="col-md-12">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>无发票金额(本位币)</label>
                                <div class="col-sm-8">
                                    <input class="form-control" min="0" step="1" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" v-model="detail.s_no_invoice_fee"
                                           maxlength="50" data-parsley-maxlength="50"  data-parsley-required="true" id="s_no_invoice_fee">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>有发票金额(本位币)</label>
                                <div class="col-sm-8">
                                    <input class="form-control" min="0" step="1" onkeyup="this.value=this.value.replace(/\D/g,'')" onafterpaste="this.value=this.value.replace(/\D/g,'')" v-model="detail.s_have_invoice_fee"
                                           maxlength="50" data-parsley-maxlength="50"  data-parsley-required="true" id="s_have_invoice_fee">
                                </div>
                            </div>
                        </div>

                        <div class="col-md-12">
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label" style="padding-right:5px"><span class="table-required">*</span>付款类型</label>
                                <div class="col-sm-10" style="padding-left:10px">
                                    <select class="form-control"  id="s_capital_cost_type" data-parsley-group="block-1" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label" style="padding-right:5px">事务描述</label>
                                <div class="col-sm-10" style="padding-left:10px">
                                    <textarea class="form-control" v-model="detail.s_expense_description" rows="2"></textarea>
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <label class="col-sm-2 control-label" style="padding-right:5px">附件</label>
                                <div class="col-sm-10" style="padding-left:10px">
                                    <input id="file" type="file" style="margin-top: 10px" >
                                    <div style="margin-top: 10px">
                                        <p  v-for="(file,index) in detail.files" style="font-size:16px" >
                                            <a :href=file.file_url target="_blank">
                                                <i class="glyphicon glyphicon-file"  style="font-size:22px"></i>
                                                {{file.file_name}}
                                            </a>
                                            <i class="glyphicon glyphicon-remove-sign delete_icon" v-on:click="removeImage(index,file)" ></i>
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </div>


                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-info">提交</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCASpecialExpenseControl?method='

export default {
    data: function() {
        return {
            rowData: {},
            oldRow: {},
            search_text: null,
            type:'',
            typeArr:[],
            statusArr:[],
            detail:{
                files:[]
            },
            isEdit:false,
            sExpenseCode:common.getUrlParams('sExpenseCode') ? common.getUrlParams('sExpenseCode'):null,
            userinfo: common.getStoreData('userinfo'),
            pagePara:{},
            show_return: 0
        }
    },
    name: 'transReceptionListControl',
    mounted: function() {
        let _self=this;
        _self.specialeExpenseTable = $('#specialeExpenseTable');
        _self.initData();
        _self.sForm = $('#formB');
        common.reSizeCall();

        $('#s_have_invoice_fee').on('change', async function(evt) {
            let s_have_invoice_fee = 0;
            let s_no_invoice_fee = 0;
            if (_self.detail.s_have_invoice_fee != null) {
                s_have_invoice_fee = _self.detail.s_have_invoice_fee
            }
            if (_self.detail.s_no_invoice_fee != null) {
                s_no_invoice_fee = _self.detail.s_no_invoice_fee
            }

            let total = (parseInt(s_no_invoice_fee) + parseInt(s_have_invoice_fee))
            $("#s_sum_fee").val(total);
        });

        $('#s_no_invoice_fee').on('change', async function(evt) {
            let s_have_invoice_fee = 0;
            let s_no_invoice_fee = 0;
            if (_self.detail.s_have_invoice_fee != null) {
                s_have_invoice_fee = _self.detail.s_have_invoice_fee
            }
            if (_self.detail.s_no_invoice_fee != null) {
                s_no_invoice_fee = _self.detail.s_no_invoice_fee
            }
            let total = (parseInt(s_no_invoice_fee) + parseInt(s_have_invoice_fee))
            $("#s_sum_fee").val(total);
        });

        $(function () {
            common.fileUpload(_self, $('#file'), apiUrl, function(fileInfo) {
                let convert = {} //转换字段名
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                _self.detail.files.push(convert);
                $('#file').val('');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        })
    },
    methods: {
        isNotEmpty:function (data) {
            if(data!=='' && data!==null){
                return true
            }else {
                return false
            }
        },
        queryParams: function (params) {
            let _self=this;
            params.trapply_creator_id=_self.userinfo.user_id;
            if(_self.isNotEmpty(_self.search_text)){
                params.search_text = _self.search_text;
            }
            let type= common.getSelect2Val('type');
            if(_self.isNotEmpty(type)){
                params.s_expense_type_id= type;
            }
            if(_self.sExpenseCode){
                params.s_expense_code= _self.sExpenseCode;
            }
            return JSON.stringify(params)
        },
        initTable: function () {
            let _self=this;

            let stateFormat=function (value,row) {
                for(let i of _self.statusArr){
                    if(i.id===value){
                        return i.text;
                    }
                }
            };
            let typeFormat = function (value,row) {
                for(let i of _self.typeArr){
                    if(i.id===value){
                        return i.text;
                    }
                }
            };
            let actFormatter= function (value, row) {
                if(row.s_expense_state!=='1' && row.s_expense_state!=='2'){
                    return [
                        '<a class="btn btn-primary btn-xs m-r-5 editDetail btn-info-table">编  辑</a>'+
                        '<a class="btn btn-xs btn-info btn-info-table tableDelete" style="margin-left: 5px">删  除</a>'
                    ].join('')
                }else {
                    return ''
                }
            }
            window.tableEvents = {
                'click .editDetail': (e, value, row, index) => {
                    let total = (parseInt(row.s_no_invoice_fee) + parseInt(row.s_have_invoice_fee))
                    $('#s_sum_fee').val(total).trigger('change');
                    _self.showModal(row);
                },
                'click .tableDelete': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '资金支出管理删除', apiUrl + 'remove', _self.specialeExpenseTable, row, 's_expense_id')
                }
            };
            _self.specialeExpenseTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'search',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormat('s_expense_code', '申请单编号'),
                    common.BTRowFormatWithFormatter('s_expense_type_id', '费用报销事由分类',typeFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_sum_fee', '合计金额',common.priceFormat),
                    /*common.BTRowFormatWithFormatterAlignRight('s_no_invoice_fee', '无发票金额(本位币)',common.priceFormat),
                    common.BTRowFormatWithFormatterAlignRight('s_have_invoice_fee', '有发票金额(本位币)',common.priceFormat),
                    common.BTRowFormatEdSelect2Disabled(_self, 's_capital_cost_type', '付款类型', 'CAPITALCOSTTYLE'),
                    common.BTRowFormatWithFormatter('s_expense_description', '事务描述',common.remarkFormatter),
                    common.BTRowFormatWithFE('files', '附件', common.filesFormatter),*/
                    common.BTRowFormatAlignLeft('s_expense_creator_name', '申请人'),
                    common.BTRowFormat('created_at', '创建时间'),
                    common.BTRowFormat('s_expense_confirm_time', '审批时间'),
                    common.BTRowFormatWithFormatter('s_expense_state', '申请状态',stateFormat),
                    common.BTRowFormatWithFormatterAlignLeft('s_expense_rejected_description', '驳回原因',common.remarkFormatter),
                    common.BTRowFormatEdSelect2Disabled(_self, 'paymentconfirm_state', '付款状态', 'payment_confirm_state'),
                    common.BTRowFormatWithFE('', '操作', actFormatter, tableEvents)
                   /* common.actFormatter('act', actFormatter, tableEvents)*/
                ],
                idField: 's_expense_id',
                uniqueId: 's_expense_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                undefinedText:'',
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: (field, row, $el, editable) => {
                    this.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: (field, row, oldValue, $el) => {
                    common.rowModifyWithT(this, apiUrl + 'modify', row, 'user_id', _self.specialeExpenseTable)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass(_self.specialeExpenseTable)
        },
        initData : function () {
            let _self=this;
            _self.$http.post(apiUrl + 'init', {}).then(response => {
                if(response.body.errno==0){
                    _self.statusArr=response.body.info.CHANGESTATE;
                    _self.typeArr=response.body.info.S_EXPENSE_TYPE;
                    _self.pagePara = JSON.parse(JSON.stringify(response.body.info));
                    _self.show_return = _self.$route.query.show_return;
                    console.log('show_return',_self.show_return)
                    common.initSelect2($('#s_capital_cost_type'), response.body.info.CAPITALCOSTTYLE);
                    _self.initTable();
                }else{
                    console.log(response.msg);
                }
            }, (response) => {
                common.dealErrorCommon(this, response);
            });
        },
        search: function(event) {
            let _self=this;
            _self.specialeExpenseTable.bootstrapTable("refresh", this.queryParams)
        },
        showModal:function (data) {
            let _self=this;
            _self.sForm.parsley().reset();
            if(data){
                _self.detail=data;
                _self.isEdit=true;
                $('#detailType').val(data.s_expense_type_id).trigger('change');
                $('#s_capital_cost_type').val(data.s_capital_cost_type).trigger('change');


            }else {
                _self.detail={
                    files:[]
                };
                _self.isEdit=false;
                _self.fileArr=[];
                $('#detailType').val([]).trigger('change')
                $('#s_sum_fee').val(null).trigger('change');
                $('#s_capital_cost_type').val(null).trigger('change');
            }
            $('#detailModal').modal('show');
        },
        postData:function () {
            let _self=this,url='', params=$.extend(true,{},_self.detail);
            if (_self.sForm.parsley().isValid()) {
                if(parseInt(_self.detail.s_sum_fee) < (parseInt( _self.detail.s_no_invoice_fee) + parseInt(_self.detail.s_have_invoice_fee))){
                    return common.dealWarningCommon("无发票金额加有发票金额不能大于合计金额！");
                }
                params.files=[];
                for(let i of _self.detail.files){
                    if(!i.file_id){
                        params.files.push(i);
                    }
                }
                params.s_expense_type_id=common.getSelect2Val('detailType');
                params.s_capital_cost_type=common.getSelect2Val('s_capital_cost_type');
                params.s_sum_fee=$("#s_sum_fee").val();

                if(_self.isEdit){
                    url=apiUrl + 'update';
                }else {
                    url=apiUrl + 'add';
                }
                _self.$http.post(url, params).then(response =>{
                    if(response.body.errno==0){
                        let temy=response.body.info;
                        _self.specialeExpenseTable.bootstrapTable("refresh", this.queryParams);
                        _self.setTask(temy);
                    }else {
                        console.log(response.msg)
                    }
                },(response) =>{
                    common.dealErrorCommon(response);
                })
            }
        },
        setTask:function (params) {
            let _self=this;
            _self.$http.post(apiUrl+'setTask', params).then(response =>{
                if(response.body.errno==0){
                    _self.specialeExpenseTable.bootstrapTable("refresh", this.queryParams);
                    $('#detailModal').modal('hide');
                }else {
                    common.dealErrorCommon(_self, response)
                }
            },(response) =>{
                common.dealErrorCommon(_self, response)
            })
        },
        removeImage:function (index,file) {
            let _self=this;
            _self.detail.files.splice(index, 1);
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
        }

    }
}
</script>
<style scoped>
    .detail-width{
        width: 650px!important;
    }
    .delete_icon{
        font-size:16px;
        color: red;
        cursor: pointer;
        margin-left: 10px
    }
</style>
