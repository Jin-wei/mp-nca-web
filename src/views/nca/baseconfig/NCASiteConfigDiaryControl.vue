<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">商城管理</a></li>
        <li class="active">装修日记维护列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">装修日记维护列表</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <!--<div class="form-group">
                            <input class="form-control" id="search_text" placeholder="搜索用户名、姓名、电话" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>-->
                        <div class="form-group  pull-right">
                            <button id="addM" class="btn btn-info" v-on:click="addM()">增加
                        </button>
                        </div>
                        <div class="clear"></div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk">
                    <table id="tableCase"></table>
                </div>
            </div>
        </div>
    </div>
    <div class="modal fade" id="AddModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title" v-show="isEdit==false">新增日记记录</h4>
                    <h4 class="modal-title" v-show="isEdit!=false">修改日记记录</h4>
                </div>
                <form @submit.prevent="submitDiay()" id="formA">
                    <div class="modal-body">
                        <div class="form-group">
                            <label><span class="table-required">*</span>标题</label>
                            <input class="form-control" v-model="rowData.diary_title" data-parsley-required="true" maxlength="200" data-parsley-maxlength="200">
                        </div>
                        <div class="form-group">
                            <label>副标题</label>
                            <input class="form-control" v-model="rowData.diary_subtitle" maxlength="200" data-parsley-maxlength="200">
                        </div>
                        <div class="form-group">
                            <label>内容</label>
                            <textarea type="text"  class="form-control" v-model="rowData.diary_content"></textarea>
                        </div>
                        <div class="form-group">
                            <label><span class="table-required">*</span>是否精选</label>
                            <select class="form-control" v-model="rowData.diary_selected" data-parsley-required="true">
                                <option value=""></option>
                                <option value="0">否</option>
                                <option value="1">是</option>
                            </select>
                        </div>
                        <div class="form-group">
                            <table id="tablePicture"></table>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="submit" class="btn btn-primary btn-info" v-show="isEdit==false">保存</button>
                        <button type="submit" class="btn btn-primary btn-info" v-show="isEdit!=false">修改</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCASiteConfigDiaryControl?method='

export default {
    data: function() {
        return {
            pagePara: {},
            rowData: {},
            isEdit:false
        }
    },
    name: 'OperatorControl',
    mounted: function() {
        let _self = this
        let $table = $('#tableCase')
        let $tablePicture=$('#tablePicture')

        function initTable() {
            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index,field) {
                    common.rowDeleteWithApi(_self, '日记删除', apiUrl + 'delete', $table, row, 'diary_id', initTable())
                },
                'click .tableEdite': function(e, value, row, index) {
                    _self.addM(row);
                }
            }

            function queryParams(params) {
                return JSON.stringify(params)
            }

            function operateFormatter(value, row, index) {
                return [
                    '<a class="btn btn-info btn-xs m-r-5 tableDelete" title="删除">',
                    '删除',
                    '</a>',
                    '<a class="btn btn-info btn-xs m-r-5 tableEdite" title="编辑" style="margin-left: 10px">',
                    '编辑',
                    '</a>',
                    // '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>',
                ].join('')
            }
            function selectFormatter(value, row, index) {
                if(value==0){
                    return '否'
                }else if(value==1){
                    return '是'
                }else {
                    return ''
                }
            }

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
                    common.BTRowFormat('diary_id', '日记ID'),
                    common.BTRowFormatWithFormatterAlignLeft('diary_title', '标题',common.remarkFormatter),
                    common.BTRowFormatWithFormatterAlignLeft('diary_subtitle', '副标题',common.remarkFormatter),
                    common.BTRowFormatWithFormatterAlignLeft('diary_content', '内容',common.remarkFormatter),
                    common.BTRowFormatWithFormatter('diary_selected', '是否精选',selectFormatter),
                    common.actFormatter('act',operateFormatter, tableEvents)
                ],
                idField: 'case_id',
                uniqueId: 'case_id',
                clickToSelect: true,
                showRefresh: false,
                striped: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                undefinedText:'',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'diary_id', $table)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($table)
        }


        function initImgTable() {
            function imagesFormatter(value, row, index) {
                var retString = '<div class="form-inline" role="form">'
                if (value.length > 0) {
                    retString += '<div class="form-group image-set">'
                    for (var key in value) {
                        retString += '<a class="box-image-link" href="' + value[0].file_url + '" data-lightbox="' + index + '">'
                        retString += '<img class="box-image" src="' + value[0].file_url + '"></a>'
                    }
                    retString += '</div>'
                }
                retString += '<span class="form-group fileupload-button">'
                retString += '<i class="glyphicon glyphicon-plus"></i>'
                retString += '<input class="imageupload" type="file" name="file">'
                retString += '</span></div>'
                return retString
            }
            window.imgEvents0 = {
                'change .imageupload': function (e, value, row, index, field) {
                    _self.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $tablePicture, 'diary_id', 0)
                }
            },
                window.imgEvents1 = {
                    'change .imageupload': function (e, value, row, index, field) {
                        _self.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $tablePicture, 'diary_id', 1)
                    },
                },
                window.imgEvents2 = {
                    'change .imageupload': function (e, value, row, index, field) {
                        _self.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $tablePicture, 'diary_id', 2)
                    }
                },
                window.imgEvents3 = {
                    'change .imageupload': function (e, value, row, index, field) {
                        _self.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $tablePicture, 'diary_id', 3)
                    }
                },
                window.imgEvents4 = {
                    'change .imageupload': function (e, value, row, index, field) {
                        _self.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $tablePicture, 'diary_id', 4)
                    }
                },

                $tablePicture.bootstrapTable({
                columns: [
                    common.BTRowFormatWithFE('content_arr0', '头像', imagesFormatter, imgEvents0),
                    common.BTRowFormatWithFE('content_arr1', '图片1', imagesFormatter, imgEvents1),
                    common.BTRowFormatWithFE('content_arr2', '图片2', imagesFormatter, imgEvents2),
                    common.BTRowFormatWithFE('content_arr3', '图片3', imagesFormatter, imgEvents3),
                    common.BTRowFormatWithFE('content_arr4', '图片4', imagesFormatter, imgEvents4),
                ],
                height: 100,
                idField: 'diary_id',
                uniqueId: 'diary_id',
                clickToSelect: true,
                showRefresh: false,
                pagination: false,
                locale: 'zh-CN',
                undefinedText:'',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($tablePicture)
        }


        $(function() {
            initTable();
            initImgTable();
        })

    },
    methods: {
        search: function() {
            $('#tableCase').bootstrapTable("refresh")
        },
        addM: function(row) {
            let _self = this;
            if(row){
                _self.isEdit=true;
                _self.rowData=row;
                _self.content_arr=row.content_arr;
                _self.header_arr=row.header_arr;
            }else {
                _self.isEdit=false;
                _self.rowData={
                    diary_id:0,
                    content_arr0:[],
                    content_arr1:[],
                    content_arr2:[],
                    content_arr3:[],
                    content_arr4:[]
                };
                $('#usergroup_id').val(null).trigger('change')
            }
            let tableArry=[]
            tableArry.push(_self.rowData);
            $('#tablePicture').bootstrapTable('load', {
                data:  tableArry
            })
            $('#AddModal').modal('show')
        },
        submitDiay:function (event) {
            let _self = this;
            if(_self.isEdit==false){
                _self.addDiary();
            }else{
                _self.modifyDiary();
            }
        },
        addDiary: function() {
            let _self = this;
            if ($('#formA').parsley().isValid()) {
                let tableData=$('#tablePicture').bootstrapTable('getData')
                _self.rowData.content_arr=[];
                _self.rowData.content_arr.push(tableData[0].content_arr0[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr1[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr2[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr3[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr4[0]);
                _self.$http.post(apiUrl + 'add', _self.rowData).then((response) => {
                    _self.rowData = {}
                    _self.search();
                    $('#usergroup_id').val(null).trigger('change')
                    $('#formA').parsley().reset()
                    _self.files=[];
                    _self.headUploadImages=[];
                    common.dealSuccessCommon('增加成功');
                    $('#AddModal').modal('hide');
                }, (response) => {
                    console.log('add error')
                    common.dealErrorCommon(_self, response)
                })
            }
        },
        fileFileUploadRefresh: function (_self, obj, row, url, method, table, key,index) {
            let files = _self.files
            let maxsize = 2 * 1024 * 1024; //2M
            let oldRow = $.extend(true, {}, row)
            let fileTypes = new Array('pdf')
            if (files.length > 0) {
                for (let i = 0; i < files.length; i++) {
                    if (files[i].size > maxsize) {
                        alert('最大只允许上传2M的文件')
                        return
                    }

                    let formData = new FormData();
                    formData.append('file', files[i])

                    obj.$http.post(url + 'upload', formData).then((response) => {
                        if(index==0){
                            row.content_arr0=[];
                            response.data.info.srv_type=0;
                            row.content_arr0.push(response.data.info)
                        }else if(index==1){
                            row.content_arr1=[];
                            response.data.info.srv_type=1;
                            row.content_arr1.push(response.data.info)
                        }else if(index==2){
                            row.content_arr2=[];
                            response.data.info.srv_type=2;
                            row.content_arr2.push(response.data.info)
                        }else if(index==3){
                            row.content_arr3=[];
                            response.data.info.srv_type=3;
                            row.content_arr3.push(response.data.info)
                        }else if(index==4){
                            row.content_arr4=[];
                            response.data.info.srv_type=4;
                            row.content_arr4.push(response.data.info)
                        }
                        table.bootstrapTable('updateByUniqueId', {
                            id: row[key],
                            row: row
                        })
                    }, (response) => {
                        // error callback
                        exports.dealErrorCommon(obj, response)
                    })
                }
            }
        },
            modifyDiary:function () {
            let _self = this;
            if ($('#formA').parsley().isValid()) {
                let tableData=$('#tablePicture').bootstrapTable('getData')
                _self.rowData.content_arr=[];
                _self.rowData.content_arr.push(tableData[0].content_arr0[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr1[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr2[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr3[0]);
                _self.rowData.content_arr.push(tableData[0].content_arr4[0]);
                _self.$http.post(apiUrl + 'modify', _self.rowData).then((response) => {
                    _self.rowData = {}
                    _self.search();
                    $('#usergroup_id').val(null).trigger('change')
                    $('#formA').parsley().reset()
                    _self.files=[];
                    _self.headUploadImages=[];
                    common.dealSuccessCommon('修改成功');
                    $('#AddModal').modal('hide');
                }, (response) => {
                    console.log('add error')
                    common.dealErrorCommon(_self, response)
                })
            }
        }
     }

}
</script>
<style scoped>
    .clear{
        clear: both;
    }
    /*.image-content img{
        width:auto;
        height: 230px;
        max-width: 540px;
    }
    .image-set .box-image,.image-set a{
        max-height: 20px;
        max-width: 20px;
    }*/
</style>
