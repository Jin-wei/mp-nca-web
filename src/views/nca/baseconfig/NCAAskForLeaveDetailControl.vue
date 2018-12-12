<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">请假管理</a></li>
            <li class="active">请假详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                            </div>
                            <span class="panel-title">请假单</span>
                            <span style="padding-left: 30px;">申请单编号：{{ask.askforleave_id}}</span>
                            <span style="padding-left: 30px;">申请人：{{ask.askforuser_name}}</span>
                            <span style="padding-left: 30px;">申请单状态：{{ask.askforleave_state_text}}</span>

                            <!--<small style="padding-left: 20px;">{{order.askforuser_name + '|' + order.phone + '|' + order.order_address}}</small>-->
                        </div>
                    </div>



                    <!--<div class="panel-toolbar">-->
                        <!--<div class="form-inline" role="form">-->
                            <!--<div class="form-group">-->

                                <!--<div class="form-group">-->
                                    <!--<button id="addMMC" class="btn btn-info" v-on:click="addMMC"><i class="glyphicon glyphicon-plus"></i>增加</button>-->
                                <!--</div>-->
                            <!--</div>-->
                        <!--</div>-->
                    <!--</div>-->

                </div>
                <div class="panel panel-inverse">
                    <div class="panel-heading" style="height: 40px">
                        <div class="panel-heading-btn">
                            <button type="submit" class="btn btn-info btn-xs is-disabled" @click="clickDone">提交</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title" ></h4>
                    </div>
                    <div class="panel-body">

                        <div class="form-group col-sm-5">
                            <label class="col-sm-4 control-label">申请起始时间</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control is-disabled" id="start_time"  placeholder="起始时间"  />
                            </div>
                        </div>
                        <div class="form-group col-sm-5">
                            <label class="col-sm-4 control-label">申请结束时间</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control is-disabled" id="end_time"  placeholder="结束时间" />
                            </div>
                        </div>
                        <div class="form-group col-sm-5">
                            <label class="col-sm-4 control-label">请假类型</label>
                            <div class="col-sm-7">
                                <select class="form-control select2 is-disabled"  style="width:100%" id="askforleave_reason"  ></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-5">
                            <label class="col-sm-4 control-label">请假天数</label>
                            <div class="col-sm-7">
                                <input type="text" class="form-control is-disabled" id="askforleave_days" v-model="ask.askforleave_days" placeholder="请输入请假天数" />
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <label class="col-sm-4 control-label" style="width: 13.33333%">请假描述</label>
                            <div class="col-sm-8" style="width: 66.2%;">
                                <textarea class="form-control is-disabled" v-model="ask.askforleave_remark" id="askforleave_remark" placeholder="请输入请假描述"  >
                                    </textarea>
                            </div>
                        </div>
                        <div class="form-group col-sm-12">
                            <div>
                                <label class="btn btn-info btn-xs fileupload-button">上传附件
                                    <input id='designUpload' class=" form-control is-disabled" type="file" name="file">
                                </label>
                            </div>
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
                        <h4 class="panel-title">附件信息</h4>
                    </div>
                    <div class="panel-body">
                        <table id="askFilesTable"></table>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAAskForLeaveControl?method=';

function generateUploadFile(_self, fileInfo) {
    if (_self) {
        let convert = {}
        convert.file_name = fileInfo.name;
        convert.file_url = fileInfo.url;
        convert.file_type = fileInfo.type;
        _self.files.push(convert)
        console.log('success')
    }
}

import fileupload from '../../../components/common/fileUpload.vue';
export default {
    data: function() {
        return {
            pagePara:'',
            ask:{},
            // ask:{
            //     files:[]
            // },
            userinfo: common.getStoreData('userinfo'),
            upload: [],
            files: [],
            sFiles: {}
//            pagePara2:{},
//            workRow:{},
//            usergroupId: '',
//            users: [],
//            eventHub: new Vue()
        }
    },
    name: 'NCAAskForLeaveDetailControl',
    components: {
            fileupload
    },
    mounted: function() {
        let _self = this;
        let askId = _self.$route.query.askId;
        _self.ask={}
        let $fileTable = $('#askFilesTable');
        async function initData() {
            let response = await _self.$http.post(apiUrl + 'init', {});
            let retData = response.data.info;

            common.initStartDatepicker($('#start_time'),$('#end_time'));
            common.initEndDatepicker($('#end_time'),$('#start_time'));
            // common.initDateTimepicker($('#start_time'));
//            let start_time = $('#start_time').val()
//             common.initDateTimepicker($('#end_time'));
//            let end_time = $('#end_time').val()
            common.initSelect2Placeholder($('#askforleave_reason'), retData.askforleaveReason, '请选择请假类型');
            _self.pagePara = JSON.parse(JSON.stringify(retData));

            common.fileUpload(_self, $('#designUpload'), apiUrl, function(fileInfo) {
                _self.upload.push(fileInfo.name)
                let convert = {}
                convert.file_name = fileInfo.name;
                convert.file_url = fileInfo.url;
                convert.file_type = fileInfo.type;
                convert.askforleave_id = _self.$route.query.askId;
                _self.files.push(convert)
                _self.$http.post(apiUrl + 'add_fi', convert).then((response) => {
                    let retData = response.data.info;
                    common.dealSuccessCommon('文件上传成功')
                    $('#askFilesTable').bootstrapTable('insertRow', {
                        index: 0,
                        row: retData
                    })
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

                async function getTablesData() {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_files', {
                            askforleave_id: _self.$route.query.askId
                        });
                        let retdata = response.data.info;
                        $('#askFilesTable').bootstrapTable('load', {
                            data: retdata.designs
                        });
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
                getTablesData()
                console.log('success')
            });

            async function getTablesData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_files', {
                        askforleave_id: _self.$route.query.askId
                    });
                    let retdata = response.data.info;
                    $('#askFilesTable').bootstrapTable('load', {
                        data: retdata.designs
                    });
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            getTablesData(_self);
            initFileTable();
            common.reSizeCall();
            await getTaskDetail()
        }

        async function getInit() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = response.data.info
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        // $('#file').change(function() {
        //     let files = this.files;
        //     let formData = new FormData();
        //     if(files.length>0){
        //         formData.append('file', files[0]);
        //         _self.filePath = $('#file').val();
        //         _self.$http.post(apiUrl + 'upload', formData).then((response) => {
        //             if (response.data.errno == 0) {
        //                 _self.fileUrl = response.data.info.fileInfo.url;
        //             } else {
        //                 common.dealErrorCommon(_self, response)
        //             }
        //         }, (response) => {
        //             common.dealErrorCommon(_self, response)
        //         })
        //     }
        //
        // })


        async function getTaskDetail() {
            try {
                let param = {
                    askforleave_id: askId,
                    domain_id: _self.userinfo.domain_id
                }
                let response = await _self.$http.post(apiUrl + 'search', param);
                _self.ask = response.data.info.rows[0];

                $('#start_time').val(_self.ask.start_time)
                $('#end_time').val(_self.ask.end_time)


                    $('#askforleave_reason').val(_self.ask.askforleave_reason).trigger('change');

//                _self.ask.askforleave_reason=common.getSelect2Val('askforleave_reason')

                if(_self.ask.askforleave_state==1 || _self.ask.askforleave_state==2){
                    $('.is-disabled').attr("disabled",true);
                }

                if(_self.ask.askforleave_state==0){
                    _self.ask.askforleave_state_text='待提交'
                }else if(_self.ask.askforleave_state==1){
                    _self.ask.askforleave_state_text='审核中'
                }else if(_self.ask.askforleave_state==2){
                    _self.ask.askforleave_state_text='已通过'
                }else if(_self.ask.askforleave_state==3){
                    _self.ask.askforleave_state_text='已驳回'
                }

            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        function initFileTable() {
            let columns;
            columns = [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormat('askforleave_id', '文件编号'),
                common.BTRowFormatWithFE('files', '文件', common.filesFormatter)
            ]

            $fileTable.bootstrapTable('destroy');

            $fileTable.bootstrapTable({
                columns: columns,
                idField: 'askforleave_id',
                uniqueId: 'askforleave_id',
                clickToSelect: true,
                height: 200,
                locale: 'zh-CN',
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($fileTable)
        }

        async function initPage() {
            await getInit()

        }

        $(async function() {
            await initPage();
            initData();
        });


    },
    methods: {

        clickDone: function() {
            let _self = this
            let param = {
                askforleave_id: _self.ask.askforleave_id,

                askforleave_remark:$('#askforleave_remark').val(),
                askforleave_days:$('#askforleave_days').val(),
                askforleave_reason: common.getSelect2Val('askforleave_reason'),
                start_time: $('#start_time').val(),
                end_time: $('#end_time').val(),

            }

            if (!$('#start_time').val()) {
                return common.dealWarningCommon('请选择起始时间')
            }
            if (!$('#end_time').val()) {
                return common.dealWarningCommon('请选择结束时间')
            }
            if (!$('#askforleave_reason').val()) {
                return common.dealWarningCommon('请选择请假类型')
            }
            if (!$('#askforleave_days').val()) {
                return common.dealWarningCommon('请填写天数')
            }

            _self.$http.post(apiUrl + 'submit', param).then((response)=>{
                common.dealSuccessCommon('任务已提交');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },

//        removeImage:function (index,file) {
//            let _self=this;
//            _self.ask.files.splice(index, 1);
//            if(file.askforleave_id){
//                var params={
//                    askforleave_id:file.askforleave_id
//                }
//                _self.$http.post(apiUrl + 'search_files', params).then(response =>{
//                    if(response.body.errno==0){
//                    }else {
//                        console.log(response.msg)
//                    }
//                },(response) =>{
//                    common.dealErrorCommon(response);
//                })
//            }
//            $('#file').val(_self.ask.search_files)
//        }

    }
}
</script>
<style scoped>
textarea{
    width:100%;resize:none
}
</style>
