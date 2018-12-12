<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <!--<li><a href="javascript:">事务管理</a></li>-->
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">运营数据管理</a></li>
            <li class="active">装修资讯列表</li>
        </ol>
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">装修资讯列表</h4>
                    </div>
                    <div class="panel-toolbar">
                        <div class="form-inline">
                            <div class="form-group">
                                <div class="form-group">
                                    <input type="text" class="form-control" placeholder="请输入标题" id="searchText"/>
                                </div>
                                <div class="form-group">
                                    <select class="select2 form-control select-width" id="decorate_type" style="width: 150px">
                                        <option value="">请选任务类型</option>
                                        <option v-for="type in pagePara.decorateType" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                                <div class="form-group">
                                    <button  class="btn btn-info" v-on:click="searchClick">
                                        <i class="fa fa-search"></i>
                                    </button>
                                </div>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="showCreateModal">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <div class="tab-content">
                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="projectTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="createTaskModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增资讯</h4>
                    </div>
                    <form @submit.prevent="submitProject" id="formProject">
                        <div class="modal-body">
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">资讯标题</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="project_name" data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">资讯类型</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple id="decorate_type_select" data-parsley-required="true"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">资讯描述</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="project_description" maxlength="300" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">是否开售</label>
                                    <div class="col-sm-8">
                                        <select class="form-control select2" multiple id="sale_select"></select>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <label class="col-sm-4 control-label">资讯链接</label>
                                    <div class="col-sm-8">
                                        <input class="form-control" id="project_url" maxlength="100" data-parsley-maxlength="50">
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-12" style="padding-left: 5px">
                                    <label class="col-md-2 control-label">备注</label>
                                    <div class="col-md-10">
                                        <input class="form-control" id="mark" maxlength="300" data-parsley-maxlength="50">
                                    </div>
                                </div>
                                <div class="form-group col-md-12">
                                    <div class="col-md-4 control-label" style="width: 170px">
                                    <label>资讯图片：</label><span>（请选图片）</span>
                                    </div>
                                    <div class="col-md-8" style="width: 399px">
                                        <input type="file" id="file" class="form-control input-sm" accept="image/*" size="1">
                                        <input type="text" id="select1" hidden>
                                    </div>
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
        <div class="modal fade" id="fileModal1">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">导入文件</h4>
                </div>

                <div class="modal-body">
                    <div class="row">
                        <div class="form-group col-md-12">
                            <label>选择文件：</label><span>（请选文件）</span>
                            <div>
                                <input type="file" id="file1" class="form-control input-sm " size="1">
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
        <div class="modal fade" id="fileModal2">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">详情</h4>
                    </div>

                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-md-12">
                                <label>选择文件：</label><span>（请选banner图片）</span>
                                <div>
                                    <input type="file" id="file_banner" class="form-control input-sm " size="1">
                                </div>
                            </div>
                            <div class="col-md-4" v-for="b in bannerArr" style="margin-top: 10px">
                                <img v-bind:src="b.file_url" alt="" style="width:100%">
                                <button @click="rmImg(b.file_id)" style="position: absolute;top: 2px;right: 2px;background: none;border: 1px solid red;color: red;border-radius: 3px">删除</button>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-md-12">
                                <label>选择文件：</label><span>（请选描述图片）</span>
                                <div>
                                    <input type="file" id="file_des" class="form-control input-sm " size="1">
                                </div>
                            </div>
                            <div class="col-md-4" v-for="b in desImgArr" style="margin-top: 10px">
                                <img v-bind:src="b.file_url" alt="" style="width:100%">
                                <button @click="rmImg(b.file_id)" style="position: absolute;top: 2px;right: 2px;background: none;border: 1px solid red;color: red;border-radius: 3px">删除</button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc');
    const apiUrl = '/api/nca/baseconfig/NCASmallProgramControl?method=';

    export default {
        data: function() {
            return {
                pagePara: {},
                userinfo: common.getStoreData('userinfo'),
                filePath: null,
                tempFileInfo: {},
                nowModifyRow: null,
                bannerArr:[],
                desImgArr:[]
            }
        },
        name: 'NCASmallProgramControl',
        mounted: function() {
            let _self = this;
            let $table = $('#projectTable');

            function queryParams(params) {
                if ($('#searchText').val){
                    params.search_text = $('#searchText').val()
                }
                if ($('#decorate_type').val){
                    params.decorate_type = $('#decorate_type').val()
                }
                return JSON.stringify(params);
            }

            function operateImage(value, row, index) {
                if (row.file_url) {
                    return [
                        '<button class="tableImg" style="border: none;background: none;"><img style="width: 35px;height: 35px" src="'+ row.file_url + '?width=35&height=35&quality=20' +'"></button>'
                    ].join('')
                } else {
                    return [
                        '<button class="tableImg" style="border: none;background: none;font-size: 20px">+</button>'
                    ].join('')
                }

            }

            function operateBtn(value, row, index) {
                return [
                    '<button class="tableDelete btn btn-info btn-xs m-r-5">删除</button>'+
                    '<button class="tableDetail btn btn-info btn-xs m-r-5">查看</button>'
                ].join('')
            }

            function typeFormatter(value, row, index) {
                for (let c of _self.pagePara.decorateType) {
                    if (c.value === value) {
                        return c.text
                    }
                }
            }

            function dateFormatter(value, row, index) {
                var date = new Date(value);
                var dateStr = date.toLocaleString();
               return dateStr;
            }

            function saleFormatter(value, row, index) {
                if (value === '0') {
                    return '未开售'
                } else if (value === '1') {
                    return '开售'
                } else {
                    return ''
                }
            }


            window.tableEvents = {
                'click .tableDelete': function(e, value, row, index) {
                    let msg =  '您确定删除'+ row.decorate_title +'么？'
                    common.rowDeleteWithApi(_self, msg, apiUrl + 'delete', $table, row, 'decorate_id', function(){
                        $table.bootstrapTable("refresh")
                    })
                },
                'click .tableDetail': function(e, value, row, index) {
                    _self.nowModifyRow = row;
                    _self.getFile()
                    $('#fileModal2').modal('show')
                },
                'click .tableImg': function(e, value, row, index) {
                    _self.nowModifyRow = row;
                    $('#fileModal1').modal('show')
                }
            }

            function initTable() {
                let columns = [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatWithFE('file_url', '图片', operateImage, tableEvents),
                    common.BTRowFormatEdTextareaWidthAlignLeft('decorate_title', '装修资讯标题',120,5),
                    common.BTRowFormatEdTextareaWidthAlignLeft('decorate_description', '装修资讯描述',120,5),
                    common.BTRowFormatEdTextareaWidth('decorate_url', '链接',100,7),
                    common.BTRowFormatWithFormatter('decorate_type', '类型', typeFormatter),
                    common.BTRowFormatAlignLeft('decorate_create_name', '创建人'),
                    common.BTRowFormatWithFormatter('created_at', '创建时间',dateFormatter),
                    common.BTRowFormat('count', '收藏人数'),
                    common.BTRowFormatEdSelect2(_self, 'is_sale', '是否销售', 'saleType'),
                    common.BTRowFormatEdTextareaWidthAlignLeft('mark', '备注',120,5),
                    common.BTRowFormatWithFE('', '操作', operateBtn, tableEvents),
                ]
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
                    columns: columns,
                    sortOrder:'asc',
                    idField: 'decorate_id',
                    uniqueId: 'decorate_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify', row, 'decorate_id', $table)
                    }
                });
                common.changeTableClass($table)
            }


            //新增装修资讯  选择图片调用
            $('#file').change(function() {
                let files = this.files;
                if (files.length === 0) {
                    return
                }
                let formData = new FormData();
                formData.append('file', files[0]);
                _self.filePath = $('#file').val();

                _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                    if (response.data.errno === 0) {
                        _self.tempFileInfo = response.data.info
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

            });

            //修改装修资讯图片调用
            $('#file1').change(function() {
                let files = this.files;
                if (files.length === 0) {
                    return
                }
                let formData = new FormData();
                formData.append('file', files[0]);

                _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                    if (response.data.errno === 0) {
                        saveTitleImg(response.data.info.name,response.data.info.type,response.data.info.url)
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

            });

            //新增装修资讯banner图片调用
            $('#file_banner').change(function() {
                let files = this.files;
                if (files.length === 0) {
                    return
                }
                let formData = new FormData();
                formData.append('file', files[0]);

                _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                    if (response.data.errno === 0) {
                        saveFile(response.data.info.name,response.data.info.type,response.data.info.url,'2')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })

            });

            //新增装修资讯描述图片调用
            $('#file_des').change(function() {
                let files = this.files;
                if (files.length === 0) {
                    return
                }
                let formData = new FormData();
                formData.append('file', files[0]);

                _self.$http.post(apiUrl + 'upload', formData).then((response) => {
                    if (response.data.errno === 0) {
                        saveFile(response.data.info.name,response.data.info.type,response.data.info.url,'3')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            });

            function saveTitleImg (name,type,url) {
                let params = {
                    name: name,
                    type: type,
                    url: url,
                    decorate_id: _self.nowModifyRow.decorate_id,
                }
                _self.$http.post(apiUrl + 'save_title_img', params).then((response) => {
                    if (response.data.errno === 0) {
                        $('#projectTable').bootstrapTable("refresh")
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
                $('#fileModal1').modal('hide')
            }

            function saveFile (name,type,url,srv_type) {
                let params = {
                    name: name,
                    type: type,
                    url: url,
                    decorate_id: _self.nowModifyRow.decorate_id,
                    srv_type: srv_type
                }
                _self.$http.post(apiUrl + 'save_file', params).then((response) => {
                    if (response.data.errno === 0) {
                        _self.getFile()
                        common.dealSuccessCommon('上传成功')
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            }

            async function initData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'init', {});
                    _self.pagePara = response.data.info
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }

            $(async function() {
                await initData();
                common.initSelect2($('#decorate_type_select'), _self.pagePara.decorateType);
                common.initSelect2($('#sale_select'), _self.pagePara.saleType);
//                common.initSelect2($('#saleType'), _self.pagePara.employeeInfo);
                initTable()
                $('#formProject').parsley()
            });
        },
        methods: {
            showCreateModal: function () {
                $('#formProject').parsley().reset()
                $('#createTaskModal').modal('show')
                this.tempFileInfo = null;
            },
            submitProject: function() {

                let _self = this
                _self.tempFileInfo = _self.tempFileInfo ? _self.tempFileInfo : {name: '', type: '', url: ''}
                if ($('#formProject').parsley().isValid()) {
                    let param = {
                        decorate_url: $('#project_url').val(),
                        decorate_title: $('#project_name').val(),
                        decorate_description: $('#project_description').val(),
                        name: _self.tempFileInfo.name,
                        type: _self.tempFileInfo.type,
                        url: _self.tempFileInfo.url,
                        decorate_type : common.getSelect2Val('decorate_type_select'),
                        is_sale : common.getSelect2Val('sale_select'),
                        mark: $('#mark').val()
                    }
                    _self.$http.post(apiUrl + 'add', param).then((response) => {
                        let retData = response.data
                        if (retData.errno === 0) {
                            common.dealSuccessCommon('资讯添加成功')
                            $('#projectTable').bootstrapTable("refresh")
                        } else {
                            common.dealErrorCommon(_self, response)
                        }
                    },(error) =>{
                        common.dealErrorCommon(_self, error)
                    })
                    $('#createTaskModal').modal('hide')
                }
            },
            searchClick: function() {
                $('#projectTable').bootstrapTable("refresh")
            },
            rmImg:function (file_id) {
                let _self = this;
                this.$http.post(apiUrl + 'remove_file', {file_id:file_id}).then((response) => {
                    if (response.data.errno === 0) {
                        _self.getFile()
                    } else {
                        common.dealErrorCommon(_self, response)
                    }
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            },
            getFile: async function () {
                let _self = this;
                try {
                    let response = await _self.$http.post(apiUrl + 'get_img', {decorate_id: _self.nowModifyRow.decorate_id});
                    _self.bannerArr = response.data.info.bannerArr
                    _self.desImgArr = response.data.info.desImgArr
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        }
    }
</script>
<style scoped>
</style>
