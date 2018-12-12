<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">行政办公管理</a></li>
            <li class="active">请假列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">请假列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form" style="height: 34.5px">
                            <div class="form-group pull-right">
                                <button id="addMMC" class="btn btn-info " v-on:click="addM">增加</button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body">
                        <div class="tab-content">
                            <div class="tab-pane fade active in">
                                <table id="AskForLeaveTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddAskForLeaveModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增请假管理</h4>
                    </div>
                    <form @submit.prevent="submitForm" id="formAKL">
                        <div class="modal-body">
                            <div class="form-horizontal">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>申请起始时间</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="start_time" data-parsley-required="true" >
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>申请结束时间</label>
                                    <div class="col-sm-8">
                                        <input type="text" class="form-control" id="end_time" data-parsley-required="true" >
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>请假类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" id="askforleave_reason"
                                                data-parsley-required="true" data-parsley-group="block-1"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label"><span
                                            class="table-required">*</span>请假天数</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" v-model="workRow.askforleave_days"
                                               id="fixedassets_unit" data-parsley-required="true" maxlength="7"
                                               data-parsley-maxlength="7">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="margin-left:-3px;">请假描述</label>
                                    <div class="col-sm-10" style="width: 80.3%;">
                                        <textarea class="form-control" v-model="workRow.askforleave_remark" maxlength="200" data-parsley-maxlength="200"></textarea>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12">
                                    <label class="col-sm-2 control-label" style="padding-right: 3px">附件</label>
                                    <div class="col-sm-9" style="padding-left: 12px">
                                        <input id="file" type="file" style="margin-top: 10px">
                                        <div style="margin-top: 10px">
                                            <p v-for="(file,index) in detail.files" style="font-size:16px">
                                                <a :href=file.file_url target="_blank">
                                                    <i class="glyphicon glyphicon-file" style="font-size:22px"></i>
                                                    {{file.file_name}}
                                                </a>
                                                <i class="glyphicon glyphicon-remove-sign delete_icon"
                                                   v-on:click="removeImage(index,file)"></i>
                                            </p>
                                        </div>
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
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAAskForLeaveControl?method=';

export default {
    data: function() {
        return {
//            pagePara:{},
//            pagePara2:{},
            workRow:{},
//            usergroupId: '',
//            users: [],
//            eventHub: new Vue(),
            detail: {
                files: []
            }
        }
    },
    name: 'NCAAskForLeaveControl',

    mounted: function() {
        let _self = this;
        let $AskForLeaveTable = $('#AskForLeaveTable');

        function nameFormatter(value, row) {
            return [
                '<a class="show_detail" style="cursor: pointer" title="查看">',
                value,
                '</a>'
            ].join('')
        }


        function operateFormatter(value, row, index) {
            if(row.askforleave_state==='1'){
                return [
                    ''
                ].join('')
            }else {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 delete_meeting">删除</a>'
                ].join('')
            }
        }
        window.tableEvents = {
            'click .delete_meeting': function (e, value, row, index) {
                common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete', $AskForLeaveTable, row, 'askforleave_id', function() {})
            },
            'click .show_detail': function(e, value, row, index) {
                    _self.$router.push({
                        path: '/nca/baseconfig/NCAAskForLeaveDetailControl',
                        query: {
                            askId: row.askforleave_id
                        }
                    })
            },
        };


        function initFitOrderTable() {
            let columsArr = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatWithFE('askforleave_id', '申请单编号',nameFormatter, tableEvents),
                common.BTRowFormatAlignLeft('askforuser_name', '申请人'),
                common.BTRowFormat('created_at', '创建时间'),
                common.BTRowFormat('check_time', '审批时间'),
                common.BTRowFormatWithFormatterAlignLeft('askforleave_refuse_remark', '驳回说明',common.remarkFormatter),
                common.BTRowFormatEdSelect2Disabled(_self, 'askforleave_state', '状态','askforleaveState'),
                common.BTRowFormatWithFE('', '操作', operateFormatter, tableEvents)//自定义方法，添加操作按钮
            ];
            $AskForLeaveTable.bootstrapTable({
                method: 'POST',//请求方式
                url: apiUrl + 'search',    //请求后台的URL
                queryParams: {},//传递参数
                sidePagination: 'server',//分页方式：client客户端分页，server服务端分页
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: function(res) {
                    return res.info;
                },
                height: common.getTableHeight(),//行高，如果没有设置height属性，表格自动根据记录条数觉得表格高度
                columns: columsArr,
                idField: 'askforleave_id',
                uniqueId: 'askforleave_id',  //每一行的唯一标识，一般为主键列
                striped: true,
                clickToSelect: true, //是否启用点击选中行
                showRefresh: false, //刷新
                pagination: true,//是否显示分页
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },

                onEditableSave: async function(field, row, oldValue, $el) {
                    let response = await _self.$http.post(apiUrl + 'modify', row);
                }
            });
            common.changeTableClass($AskForLeaveTable)
        }

        async function initData() {

            let startDate = $('#start_time');
            let endDate = $('#end_time');
            common.initStartDatepicker(startDate,endDate);
            common.initEndDatepicker(endDate,startDate);

            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;
            _self.pagePara = JSON.parse(JSON.stringify(retData));
//            let askforuser_id = _self.pagePara.userInfo.askforuser_id

//            common.initSelect2($('#vehicle_type'), retData.vehicleType);
//            common.initSelect2Placeholder($('#vehicle_status'), retData.vehicleStatus,'状态');
//            common.initSelect2Placeholder($('#vehicle_type2'), retData.vehicleType, '车辆种类');
            common.initSelect2($('#askforleave_reason'), retData.askforleaveReason);
            initFitOrderTable();
            common.reSizeCall();
            $('#formAKL').parsley()

//            $('#for`mVMC').parsley()
        }




        $(function() {
            initData();

            common.fileUpload(_self, $('#file'), apiUrl, function (fileInfo) {
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

        addM: function(event) {
            // let _self = this;
            // let params={askforleave_id:''};
            // common.dealConfrimCommon('确认新增请假申请单？', async () => {
            //     _self.$http.post(apiUrl + 'add', params).then(response => {
            //         $('#AskForLeaveTable').bootstrapTable("refresh");
            //     }, (response) => {
            //         common.dealErrorCommon(this, response);
            //     });
            // });
            let _self = this;
            _self.workRow = {};
            $('#askforleave_reason').val(null).trigger('change');
            $('#start_time').val(null).trigger('change');
            $('#end_time').val(null).trigger('change');
            _self.detail = {
                files: []
            }
            $('#AddAskForLeaveModal').modal('show');
        },
        submitForm: async function(event) {
            let _self = this, params = $.extend(true, {}, _self.detail);
            _self.workRow.files = [];
            for (let i of _self.detail.files) {
                if (!i.file_id) {
                    _self.workRow.files.push(i);
                }
            }
            if ($('#formAKL').parsley().isValid()) {
                try {
                    _self.workRow.start_time = $('#start_time').val();
                    _self.workRow.end_time = $('#end_time').val();
                    _self.workRow.askforleave_reason = common.getSelect2Val('askforleave_reason');

                    let response = await _self.$http.post(apiUrl + 'add', _self.workRow);
                    $('#AskForLeaveTable').bootstrapTable("refresh");
                    _self.workRow = {};
                    console.log('add success');
                    $('#formAKL').parsley().reset();
                    $('#AddAskForLeaveModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }
        },
        removeImage: function (index, file) {
            let _self = this;
            _self.detail.files.splice(index, 1);
            if (file.file_id) {
                var params = {
                    file_id: file.file_id
                }
                _self.$http.post(apiUrl + 'removeFile', params).then(response => {
                    if (response.body.errno == 0) {
                    } else {
                        console.log(response.msg)
                    }
                }, (response) => {
                    common.dealErrorCommon(response);
                })
            }
        }
    }
}

</script>
<style scoped>
textarea{
    width:100%;resize:none
}
</style>
