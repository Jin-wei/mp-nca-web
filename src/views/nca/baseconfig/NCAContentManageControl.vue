<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">系统管理</a></li>
            <li class="active">内容信息列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">内容信息列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <select class="form-control select2" multiple id="locationSearch"></select>
                            </div>
                            <div class="form-group" id="search_div">
                                <input class="form-control" multiple id="search_text" placeholder="搜索标题 ">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group  pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="addM">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height hidedesk">
                        <table id="contentTable"></table>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade " id="AddModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 v-if="saveId == ''" class="modal-title">新增内容列表</h4>
                        <h4 v-else class="modal-title">编辑内容列表</h4>
                    </div>
                    <form @submit.prevent="addType" id="formA">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-xs-6">
                                    <label><span class="table-required">*</span>位置:</label>
                                    <select class="form-control select2" multiple id="contentLocationOne" data-parsley-required="true"> </select>
                                </div>
                                <div class="form-group col-xs-6">
                                    <label><span class="table-required">*</span>标题:</label>
                                    <input class="form-control" id="contentTitle" v-model='work.title' data-parsley-required="true" maxlength="100" data-parsley-maxlength="100">
                                </div>
                            </div>

                            <div class="row">
                                <div class="form-group col-xs-6">
                                    <label>副标题:</label>
                                    <input class="form-control" id="contentTitleAssist" v-model='work.titleAssist' maxlength="100" data-parsley-maxlength="100">
                                </div>
                                <div class="form-group col-xs-6">
                                    <label>完工日期:</label>
                                    <input class="form-control" id="completion_date" >
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-xs-12">
                                    <label>简介:</label>
                                    <input class="form-control" id="contentSynopsis" v-model='work.abstract' maxlength="100" data-parsley-maxlength="100">
                                </div>
                                <div class="form-group col-xs-12">
                                    <label>缩略图:</label>
                                    <input type="file" id="thumbnailFile" name="file" style="display: inline-block;"/>
                                    <span>{{hint}}</span>
                                    <div v-for="(u,index) in uploadFiles" style="display: inline-block">
                                        <img class='thumbnail' id="thumbnailImg"  v-bind:src="u">
                                        <button type="button" class="btn btn-info deleteImg" v-on:click="removeAddon(index)">删除</button>
                                    </div>
                                </div>
                                <div class="form-group col-xs-12">
                                    <label>缩略图跳转地址:</label>
                                    <input class="form-control" id="jump_url" v-model='work.jump_url' maxlength="100" data-parsley-maxlength="100">
                                </div>
                                <div class="form-group col-xs-12">
                                    <label>生成图片链接:</label>
                                    <input type="file" name="file" id="createdImgFile"/>
                                </div>
                                <div class="form-group col-xs-12">
                                    <input type="text" class="form-control" v-model="imageUrl">
                                </div>

                                <div class="form-group col-xs-12">
                                    <label>内容:</label>
                                    <div id="textEditor"></div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-xs-12">
                                    <label for="topContent">是否显示:</label>
                                    <input type='checkbox' class='roomTypeCheckBox' :checked="top" v-model="top" id="topContent" >
                                </div>
                            </div>

                        </div>
                        <div class="modal-footer">
                            <button type="submit" class="btn btn-info">保存</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/baseconfig/NCACms?method=';
    const getFileUrl = '/api/nca/baseconfig/NCACmsFile?method='
    var E = window.wangEditor;
    var editor = new E('#textEditor');
    export default {
        data: function() {
            return {
                oldRow:[],
                work:{},
                pagePara:{},
                imageUrl: '',
                fileIds:[],
                uploadFiles : [],
                saveId:'',
                top:0,
                hint:''
            }
        },
        name: 'contentManageType',

        mounted: function() {
            let _self = this;
            // 生成编辑器

            editor.create();

            this.contentTable = $('#contentTable');
            this.initPage();
            this.initTable();

            $('#contentLocationOne').on('select2:select', async function(evt) {
                let contentLocationOne = common.getSelect2Val('contentLocationOne');
                if(contentLocationOne==1){
                    _self.hint = '建议尺寸:宽1250px*高450px'
                }else if (contentLocationOne==3){
                    _self.hint = '建议尺寸:宽360px*高240px'
                }else if (contentLocationOne==4){
                    _self.hint = '建议尺寸:宽270px*高140px'
                }else {
                    _self.hint = ''
                }

            });

            $('#thumbnailFile').change(function() {
                let files = this.files;
                if (files.length > 0) {
                    for (let i = 0; i < files.length; i++) {
                        let formData = new FormData();
                        formData.append('file', files[i]);
                        _self.$http.post(apiUrl+'upload_f', formData).then((response) => {
                            if (response.data.errno == 0) {
                                _self.uploadFiles.push(getFileUrl+'get_f&fileId='+response.data.info._id);
                                _self.fileIds.push(response.data.info._id);
                                $('#file').val('');
                            } else {
                                common.dealErrorCommon(_self, response)
                            }
                        }, (response) => {
                            common.dealErrorCommon(_self, response)
                        })
                    }
                }
            });

            $('#createdImgFile').change(function() {
                let files = this.files;
                if (files.length > 0) {
                    let formData = new FormData();
                    formData.append('file', files[0]);
                    _self.$http.post(apiUrl+'upload_f', formData).then((response) => {
                        if (response.data.errno == 0) {
                            _self.imageUrl = getFileUrl+'get_f&fileId='+response.data.info._id;
                            $('#file').val('');
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    }, (response) => {
                        common.dealErrorCommon(_self, response)
                    })

                }
            })
        },
        methods: {
            queryParams: function (params) {
                params.parentId=1;
                params.active=1;
                params.title=$('#search_text').val();
                params.pt=common.getSelect2Val('locationSearch');
                return JSON.stringify(params)
            },
            rowDelete: function (msg, apiUrl, row, key) {
                common.dealConfrimCommon('删除', () => {
                    let params = {
                        contentId:row._id,
                        contentInfo:row
                    }
                    params.contentInfo.active = 0;
                    this.$http.post(apiUrl + 'update_c', params).then(response => {
                        this.contentTable.bootstrapTable("refresh")
                        common.dealSuccessCommon('删除成功');
                    }, (response) => {
                        common.dealErrorCommon(this, response);
                    });
                });
            },
            initTable: function () {
                let _self = this;
                console.log(123123213)
                function topFormatter(value, row, index) {
                    if(value==1){
                        return '是';
                    }
                }
                window.tableEvents = {
                    'click .contentDetail': function(e, value, row, index) {
                        _self.$router.push({
                            path: '/nca/baseconfig/NCAContentManageDetailControl',
                            query: {
                                _id: row._id
                            }
                        })
                    },
                    'click .contentEdit': function(e, value, row, index) {
                        _self.initForm();
                        _self.saveId = row._id;
                        $('#contentLocationOne').val(row.pt).trigger('change');
                        _self.work.title = row.title;
                        _self.work.titleAssist = row.author;
                        _self.work.abstract = row.abstract;
                        for(let a of row.addons){
                            _self.uploadFiles.push(getFileUrl+'get_f&fileId='+ a);
                            _self.fileIds.push(a);
                        }
                        editor.txt.html(row.content);
                        _self.top = row.top;
                        $('#completion_date').val(row.completion_date);
                        _self.work.jump_url = row.jump_url;
                        $('#AddModal').modal('show')
                    },
                    'click .contentDelete': async function(e, value, row, index) {
                        _self.rowDelete('删除', apiUrl, row, 'landagent_id');
                    },

                };
                this.contentTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'get_c',
                    queryParams: this.queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatEdSelect2Disabled(this, 'pt', '位置', 'content_location_one'),
                        common.BTRowFormatWithFormatterAlignLeft('title', '标题',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('author', '副标题',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('abstract', '简介',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('top', '是否显示', topFormatter),
                        common.actFormatter('act', () => {
                            return [
                             '<a class="btn btn-info btn-xs m-r-5 contentEdit">编辑</a>',
                             '<a class="btn btn-info btn-xs m-r-5 contentDetail">查看</a>',
                             '<a class="btn btn-info btn-xs m-r-5 contentDelete">删除</a>'
                            ].join('')
                        }, tableEvents)
                    ],
                    idField: '_id',
                    uniqueId: '_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onPostBody: function() {
                    $('[data-toggle="popover"]').each(function() {
                        $(this).popover()
                    })
                    }
                })
                common.changeTableClass(this.contentTable)
            },
            initPage:async function () {
                $('#formMod').parsley();
                let response = await this.$http.post(apiUrl + 'init', {});
                let retData = response.data.info;
                this.pagePara = response.data.info;
                common.initSelect2($('#contentLocationOne'), retData.content_location_one);
                common.initDatepicker($('#completion_date'));
                common.initSelect2Placeholder($('#locationSearch'), retData.content_location_one, '位置');
            },

            search: function(event) {
                this.contentTable.bootstrapTable("refresh", this.queryParams)
            },
            initForm:function(){
                $('#formA').parsley().reset();
                this.uploadFiles=[];
                this.work={};
                $('#contentLocationOne').val(null).trigger('change');
                $('#file').val('');
                editor.txt.html('');
                this.fileIds=[];
                $('#completion_date').val('');
                this.top=1;
                $('#createdImgFile').val('');
                this.imageUrl = '';
            },
            removeAddon:function(index){
                this.uploadFiles.splice(index,1);
                this.fileIds.splice(index,1);
            },
            addM: function(event) {
                this.initForm();
                this.saveId = 0;
                $('#AddModal').modal('show')
            },
            addType: async function(event) {
                let params = {};
                if ($('#formA').parsley().isValid()) {
                    if(this.saveId){
                        params.contentInfo = {};
                        params.contentId = this.saveId;
                        params.contentInfo.pt = common.getSelect2Val('contentLocationOne');//位置
                        params.contentInfo.title = this.work.title;//标题
                        params.contentInfo.content = editor.txt.html();//附文本
                        params.contentInfo.abstract = this.work.abstract;//简介
                        params.contentInfo.author = this.work.titleAssist;//副标题
                        params.contentInfo.addons = this.fileIds;
                        params.contentInfo.parentId = 1;
                        params.contentInfo.active = 1;
                        params.contentInfo.completion_date = $("#completion_date").val();//完工日期
                        params.contentInfo.top = this.top;
                        params.contentInfo.jump_url = this.work.jump_url;
                        console.log('params:'+params);
                        let response = await this.$http.post(apiUrl + 'update_c', params);
                    }else{
                        params.pt = common.getSelect2Val('contentLocationOne');//位置
                        params.title = this.work.title;//标题
                        params.content = editor.txt.html();//附文本
                        params.abstract = this.work.abstract;//简介
                        params.author = this.work.titleAssist;//副标题
                        params.addons = this.fileIds;
                        params.parentId = 1;
                        params.active = 1;
                        params.completionDate = $("#completion_date").val();//完工日期
                        params.top = this.top;
                        params.jump_url = this.work.jump_url;
                        console.log('params:'+params);
                        let response = await this.$http.post(apiUrl + 'add_c', params);
                    }

                    this.contentTable.bootstrapTable('refresh');
                    this.initForm();
                    $('#AddModal').modal('hide');
                }
            }
        }
    }
</script>
<style scoped>
.thumbnail{
    margin-top: 10px;width: 70px;height: 70px;
    display:inline-block;margin-left: 10px
}
    .deleteImg{
        display: block;
        position: relative;
        top: -15px;
        left: 15px;
        width: 62px;
        height: 30px;
    }
</style>
