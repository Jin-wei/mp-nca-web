<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;">商城管理</a></li>
        <li class="active">案例维护列表</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">案例维护列表</h4>
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
                            <button id="addM" class="btn btn-info" v-on:click="addM">增加
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
                    <h4 class="modal-title">新加案例记录</h4>
                </div>
                <form @submit.prevent="addCase" id="formA">
                    <div class="modal-body">
                        <div class="form-group">
                            <label><span class="table-required">*</span>标题</label>
                            <input class="form-control" v-model="rowData.case_title" data-parsley-required="true" maxlength="200" data-parsley-maxlength="200">
                        </div>
                        <div class="form-group">
                            <label><span class="table-required">*</span>副标题</label>
                            <input class="form-control" v-model="rowData.case_subtitle" data-parsley-required="true" maxlength="200" data-parsley-maxlength="200">
                        </div>
                        <div class="form-group">
                            <label>内容</label>
                            <textarea type="text"  class="form-control" v-model="rowData.case_content"></textarea>
                        </div>
                        <!--<div class="form-group">
                            <label>封面图片</label>
                            <input class="form-control" v-model="rowData.phone" data-parsley-phone="true">
                        </div>-->
                        <div class="form-group ">
                            <div>
                                <label class="btn btn-info btn-xs fileupload-button">上传封面
                                    <input id='acceptanceUpload' type="file" name="file">
                                </label>
                            </div>
                        </div>
                        <div class="form-group ">
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
                        <button type="submit" class="btn btn-primary btn-info">保存</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCASiteConfigCaseControl?method='

export default {
    data: function() {
        return {
            pagePara: {},
            rowData: {},
            oldRow: {},
            uploadImages: [], //记录上传图片的url,用于显示
            files: [], //记录上传图片的file信息，Object类型
            uploadFiles: [],
            nowFileUrl: null,
            nowDesignId: null,
            order:''
        }
    },
    name: 'OperatorControl',
    mounted: function() {
        let _self = this
        let $table = $('#tableCase')

        function initTable() {
            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    common.rowDeleteWithApi(_self, '案例删除', apiUrl + 'delete', $table, row, 'case_id', initTable())
                }
            }
            window.imgEvents = {
                'change .fileupload': function(e, value, row, index) {
                    common.fileFileUploadRefresh(this, _self, row, apiUrl, 'modify_img', $table, 'case_id')

                },
                'click .delete-button': function(e, value, row, index) {
                    common.deleteFilesRRefresh(this, _self, row, apiUrl, 'delete_img', $table, 'case_id')
                },
            }

            function queryParams(params) {
                return JSON.stringify(params)
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
                    common.BTRowFormat('case_id', '案例ID'),
                    common.BTRowFormatEditableLeft('case_title', '标题'),
                    common.BTRowFormatEditableLeft('case_subtitle', '副标题'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('case_content', '内容',300,35),
                    common.BTRowFormatWithFE('files', '封面', common.filesFormatterWithUpload, imgEvents),
                    common.actFormatter('act', ()=>{
                        return [
                            '<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>'
                        ].join('')
                    }, tableEvents)
                ],
                idField: 'case_id',
                uniqueId: 'case_id',
                clickToSelect: true,
                showRefresh: false,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN',
                undefinedText:'',
                onEditableShown: function(field, row, $el, editable) {
                    _self.oldRow = $.extend(true, {}, row)
                },
                onEditableSave: function(field, row, oldValue, $el) {
                    common.rowModifyWithT(_self, apiUrl + 'modify', row, 'case_id', $table)
                },
                onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                }
            })
            common.changeTableClass($table)
        }

        $(function() {
            initTable()
            common.fileUpload(_self, $('#acceptanceUpload'), apiUrl, function(fileInfo) {
                _self.uploadImages=[];
                _self.files=[];
                _self.uploadImages.push(fileInfo.url)
                let convert = {} //转换字段名
                convert.file_name = fileInfo.name
                convert.file_url = fileInfo.url
                convert.file_type = fileInfo.type;
                _self.files.push(convert)
                $('#acceptanceUpload').val('');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        })

    },
    methods: {
        search: function(event) {
            $('#tableCase').bootstrapTable("refresh")
        },
        addM: function(event) {
            let _self = this
            _self.rowData = {}
            $('#usergroup_id').val(null).trigger('change')
            $('#AddModal').modal('show')
        },
        addCase: function(event) {
            let _self = this
            if ($('#formA').parsley().isValid()) {
                _self.rowData.img_arr=_self.files;
                _self.$http.post(apiUrl + 'addCase', _self.rowData).then((response) => {
                    _self.rowData = {}
                    _self.search();
                    $('#usergroup_id').val(null).trigger('change')
                    $('#formA').parsley().reset()
                    _self.files=[];
                    _self.uploadImages=[];
                    common.dealSuccessCommon('增加成功');
                    $('#AddModal').modal('hide');
                }, (response) => {
                    console.log('add error')
                    common.dealErrorCommon(_self, response)
                })
            }
        },

        removImage: async function(index) {
            let _self = this
            _self.uploadImages.splice(index, 1)
            _self.files.splice(index, 1)
            /*let params = {fileIds:[]};
            params.fileIds.push(_self.uploadImages.file_id);
            if (params.fileIds.length > 0) {
                _self.$http.post(apiUrl + 'deleteImg', params).then((response) => {

                }, (response) => {
                    // error callback
                    exports.dealErrorCommon(_self, response)
                })
            }*/

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
