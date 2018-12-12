<template>
<div>
    <!-- begin breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <button type="button" class="btn btn-info btn-xs" v-show="userDetail.state == 1" @click="clickDone">保存</button>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">员工信息详情</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="col-sm-10">
                        <div class="container" id="crop-avatar" style="padding-left: 0;">
                            <!-- Current avatar -->
                            <div class="avatar-view" title="" data-original-title="Change the avatar" style="height:130px;width:130px;">
                                <img :src="userinfo.avatar" alt="Avatar" v-show="userDetail.standard_img ==''" style="height:130px;width:130px;">
                                <img :src="userDetail.standard_img" alt="Avatar" v-show="userDetail.standard_img !=''" style="height:130px;width:130px;">
                            </div>
                            <p style="color: red;padding-top: 10px;">注意：最大只允许上传2M的文件</p>
                            <div class="modal fade" id="avatar-modal" aria-hidden="true" aria-labelledby="avatar-modal-label" role="dialog" tabindex="-1" style="display: none;">
                                <div class="modal-dialog modal-lg">
                                    <div class="modal-content">
                                        <form class="avatar-form" enctype="multipart/form-data" method="post">
                                            <div class="modal-header">
                                                <button class="close" data-dismiss="modal" type="button">×</button>
                                                <h4 class="modal-title" id="avatar-modal-label">更换头像</h4>
                                            </div>
                                            <div class="modal-body">
                                                <div class="avatar-body">
                                                    <!-- Upload image and data -->
                                                    <div class="avatar-upload">
                                                        <input class="avatar-src" name="avatar_src" type="hidden">
                                                        <input class="avatar-data" name="avatar_data" type="hidden" value="">
                                                        <label for="avatarInput">头像上传</label>
                                                        <input class="avatar-input" id="avatarInput" name="avatar_file" type="file">
                                                    </div>

                                                    <!-- Crop and preview -->
                                                    <div class="row">
                                                        <div class="col-md-9">
                                                            <div class="avatar-wrapper"></div>
                                                        </div>
                                                        <div class="col-md-3">
                                                            <div class="avatar-preview preview-lg" style="width: 184px; height: 184px;"></div>
                                                            <div class="avatar-preview preview-md" style="width: 100px; height: 100px;"></div>
                                                            <div class="avatar-preview preview-sm" style="width: 50px; height: 50px;"></div>
                                                        </div>
                                                    </div>

                                                    <div class="row avatar-btns">
                                                        <div class="col-md-3">
                                                            <button class="btn btn-info avatar-save" type="submit">完成</button>
                                                        </div>
                                                    </div>
                                                </div>
                                            </div>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;margin-top: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">身份证号</span>
                                <input class="form-control" v-model="userDetail.idcarde_no" id="idcarde_no" maxlength="18" data-parsley-maxlength="18">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">出生日期</span>
                                <input type="text" class="form-control" id="birth_date">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">年龄</span>
                                <input class="form-control" v-model="userDetail.age" id="age" maxlength="3" data-parsley-maxlength="3" disabled>
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">婚姻状况</span>
                                <div>
                                    <select class="form-control select2" style="width:100%" id="marital_status"></select>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">学历</span>
                                <select class="form-control select2" style="width:100%" id="education"></select>
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">毕业院校</span>
                                <input class="form-control" v-model="userDetail.graduate_institution" id="graduate_institution">
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">毕业日期</span>
                                <input type="text" class="form-control" id="graduate_date">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">籍贯</span>
                                <input class="form-control" v-model="userDetail.native_place" id="native_place">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">民族</span>
                                <div>
                                    <select class="form-control select2" style="width:100%" id="ethnicity"></select>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">户口类型</span>
                                <div>
                                    <select class="form-control select2" style="width:100%" id="register_category"></select>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">现居住地</span>
                                <input class="form-control" v-model="userDetail.living_place" id="living_place">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">个人工龄</span>
                                <input class="form-control" v-model="userDetail.service_length" id="service_length" oninput="if(value.length>2)value=value.slice(0,2)">
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">司龄</span>
                                <input class="form-control" v-model="userDetail.working_age" id="working_age" oninput="if(value.length>2)value=value.slice(0,2)">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">紧急联系人</span>
                                <input class="form-control" v-model="userDetail.emergency_contact_person" id="emergency_contact_person">
                            </div>
                        </div>
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">联系电话</span>
                                <input class="form-control" v-model="userDetail.emergency_contact_phone" id="emergency_contact_phone" data-parsley-required="true" data-parsley-phone="true">
                            </div>
                        </div>
                    </div>
                    <div class="row" style="margin-bottom: 9px;">
                        <div class="form-group col-sm-4">
                            <div class="input-group">
                                <span class="input-group-addon">联系人QQ</span>
                                <input class="form-control" v-model="userDetail.emergency_contact_qq" id="emergency_contact_qq">
                            </div>
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
                        <button id="addP" class="btn btn-info btn-xs" @click="addP">增加</button>
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">照片信息</h4>
                </div>
                <div class="panel-body">
                    <table id="personalTable"></table>
                </div>
            </div>
        </div>
        <div class="modal fade" id="PersonalModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增照片记录</h4>
                    </div>
                    <form @submit.prevent="saveForm" id="formEP">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label">标题</label>
                                <div class="col-sm-8">
                                    <input class="form-control" placeholder="生活照" disabled>
                                </div>
                            </div>

                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>上传格式</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" id="goupload_format" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12">
                                <div style="margin-left: 30px">
                                    <label class="btn btn-info btn-xs fileupload-button">上传生活照
                                        <input id='goacceptanceUpload' type="file" name="file">
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
                            <button type="submit" class="btn btn-info">确认</button>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const CryptoJS = require('crypto-js')
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';

function imageArrayToString(array) {
    return array.length > 0 ? array.join('|') : ''
}

function stringToArray(string) {
    return string.length > 0 ? string.split('|') : []
}

export default {
    props: ['employee', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            oldRow: {},
            userDetail: {},
            postArray: {},
            retdata: {},
            userinfo: common.getStoreData('userinfo'),
            avatar: '',
            uploadImages: [], //记录上传图片的url,用于显示
            files: [], //记录上传图片的file信息，Object类型
        }
    },
    name: 'NCAEPersonalInformationControl',
    watch: {
        pagePara: function() {
            let _self = this;
            if($('#birth_date').val()){
                params.birth_date = $('#birth_date').val()
            }
            if($('#graduate_date').val()){
                params.graduate_date = $('#graduate_date').val()
            }
            common.initDatepicker($('#birth_date'));
            common.initDatepicker($('#graduate_date'));
            common.initSelect2($('#marital_status'), _self.pagePara.maritalStatus);
            common.initSelect2($('#ethnicity'), _self.pagePara.nationalInfo);
            common.initSelect2($('#register_category'), _self.pagePara.registerCategory);
            common.initSelect2($('#education'), _self.pagePara.educationInfo);
            common.initSelect2($('#goupload_format'), _self.pagePara.acceptanceInfo);
        },
        employee: function() {
            let _self = this;
            let $table = $('#personalTable');

            function getData() {
                _self.$http.post(apiUrl + 'search_d', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info.userDetail;
                    let userDetail = response.data.info.userDetail[0];
                    _self.userDetail = JSON.parse(JSON.stringify(userDetail))
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }
            function getData2() {
                _self.$http.post(apiUrl + 'search_di', {
                    user_id: _self.employee.userDetail[0].user_id
                }).then((response) => {
                    let retdata = response.data.info.rows[0]
                    _self.retdata = JSON.parse(JSON.stringify(retdata))
                    let retdata2 = retdata.images.lenght
                    if (retdata != 0) {
                        $('#addP').prop('disabled', true);
                    }

                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }
//            function initData() {
//                let mValue = $('#p_usergroup_id').val()
//                if (mValue) {
//                    _self.$http.post(apiUrl + 'usergroup', {
//                        p_usergroup_id: mValue
//                    }).then(response => {
//                        _self.postArray = response.data.info.groupList;
//                        common.initSelect2($('#usergroup_id'), _self.postArray);
//                        $('#usergroup_id').val(_self.postArray.usergroup_id).trigger('change');
//                    }, (response) => {
//                        common.dealErrorCommon(_self, response);
//                    });
//                }
//            }
            function queryParams(params) {
                common.initImageViewer()
                params.user_id = _self.employee.userDetail[0].user_id
                return JSON.stringify(params);
            }

//            function editFormatter(value, row) {
//                return [
//                   '<a class="btn btn-primary btn-xs m-r-5 tableEdit btn-primary">编辑</a>' +
//                    '<a class="btn btn-primary btn-xs m-r-5 tableDelete btn-info-delete">删除</a>'
//                ].join('')
//            }

            function acceptanceFormatter(value, row, index) {
                let images = row.images
                let retString = '<div class="form-inline image" role="form">'
                for (let i = 0; i < images.length; i++) {
                    let url = images[i].file_url
                    retString += '<sapn><img class="icon-size" src="' + url + '"></sapn>'
                }
                retString += '</div>'
                return retString
            }

            function initTable() {
                window.tableEvents = {
//                    'click .tableDelete': function(e, value, row, index) {
//                        common.rowDeleteWithApi(_self, '删除', apiUrl + 'delete_a', $table, row, 'user_id', function() {})
//                    },
//                    'click .tableEdit': function (e, value, row, index) {
//                        _self.oldRow = $.extend(true, {}, row)
//                        _self.workRow = $.extend(true, {}, row)
//                        $('#goupload_format').val(_self.workRow.goupload_format).trigger('change')
//                        _self.uploadImages = []
//                        for (let i = 0; i < _self.workRow.images.length; i++) {
//                            let image = _self.workRow.images[i]
//                            _self.uploadImages.push(image.file_url)
//                        }
//                        _self.files = _self.workRow.images
//                        $('#propertyAcceptanceModal').modal('show')
//                    }
                };
                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_di',
                    queryParams: queryParams,
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFormatterAlignLeft('photo_title', '标题',common.remarkFormatter),
                        common.BTRowFormatEdSelect2Disabled(_self, 'goupload_format', '上传格式', 'acceptanceInfo'),
                        common.BTRowFormatWithFormatter('images', '上传实例', acceptanceFormatter),
//                        common.actFormatter('edit', editFormatter, tableEvents),
                    ],
                    idField: 'user_id',
                    uniqueId: 'user_id',
                    clickToSelect: true,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    striped: true,
                    onEditableShown: function(field, row, $el, editable) {
                        _self.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: function(field, row, oldValue, $el) {
                        common.rowModifyWithT(_self, apiUrl + 'modify_a', row, 'user_id', $table)
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($table)
            }
            $('#ethnicity').val(_self.employee.userDetail[0].ethnicity).trigger('change');
            $('#marital_status').val(_self.employee.userDetail[0].marital_status).trigger('change');
            $('#register_category').val(_self.employee.userDetail[0].register_category).trigger('change');
            $('#education').val(_self.employee.userDetail[0].education).trigger('change');
            $('#birth_date').val(_self.employee.userDetail[0].birth_date).trigger('change');
            $('#graduate_date').val(_self.employee.userDetail[0].graduate_date).trigger('change');
//            initData()
            getData()
            getData2()
            async function initPage() {
                initTable()
            }
            $(function() {
                initPage();
                common.fileUpload(_self, $('#goacceptanceUpload'), apiUrl, function(fileInfo) {
                    _self.uploadImages.push(fileInfo.uploadurl.url)
                    let convert = {}
                    convert.file_name = fileInfo.uploadurl.name
                    convert.file_url = fileInfo.uploadurl.url
                    convert.file_type = fileInfo.uploadurl.type
                    _self.files.push(convert)
                }, (response) => {
                    common.dealErrorCommon(_self, response)
                })
            });
        }
    },
    mounted: function() {
        let _self = this

        function CropAvatar($element) {
            this.$container = $element;

            this.$avatarView = this.$container.find('.avatar-view');
            this.$avatar = this.$avatarView.find('img');
            this.$avatarModal = this.$container.find('#avatar-modal');
            this.$loading = this.$container.find('.loading');

            this.$avatarForm = this.$avatarModal.find('.avatar-form');
            this.$avatarUpload = this.$avatarForm.find('.avatar-upload');
            this.$avatarSrc = this.$avatarForm.find('.avatar-src');
            this.$avatarData = this.$avatarForm.find('.avatar-data');
            this.$avatarInput = this.$avatarForm.find('.avatar-input');
            this.$avatarSave = this.$avatarForm.find('.avatar-save');
            this.$avatarBtns = this.$avatarForm.find('.avatar-btns');

            this.$avatarWrapper = this.$avatarModal.find('.avatar-wrapper');
            this.$avatarPreview = this.$avatarModal.find('.avatar-preview');

            this.init();
        }

        CropAvatar.prototype = {
            constructor: CropAvatar,

            support: {
                fileList: !!$('<input type="file">').prop('files'),
                blobURLs: !!window.URL && URL.createObjectURL,
                formData: !!window.FormData
            },

            init: function() {
                this.support.datauri = this.support.fileList && this.support.blobURLs;

                if (!this.support.formData) {
                    this.initIframe();
                }

                this.initTooltip();
                this.initModal();
                this.addListener();
            },

            addListener: function() {
                this.$avatarView.on('click', $.proxy(this.click, this));
                this.$avatarInput.on('change', $.proxy(this.change, this));
                this.$avatarForm.on('submit', $.proxy(this.submit, this));
                this.$avatarBtns.on('click', $.proxy(this.rotate, this));
            },

            initTooltip: function() {
                this.$avatarView.tooltip({
                    placement: 'bottom'
                });
            },

            initModal: function() {
                this.$avatarModal.modal({
                    show: false
                });
            },

            initPreview: function() {
                let url = this.$avatar.attr('src');

                this.$avatarPreview.empty().html('<img src="' + url + '">');
            },

            initIframe: function() {
                let target = 'upload-iframe-' + (new Date()).getTime(),
                    $iframe = $('<iframe>').attr({
                        name: target,
                        src: ''
                    }),
                    _this = this;

                // Ready ifrmae
                $iframe.one('load', function() {

                    // respond response
                    $iframe.on('load', function() {
                        let data;

                        try {
                            data = $(this).contents().find('body').text();
                        } catch (e) {
                            console.log(e.message);
                        }

                        if (data) {
                            try {
                                data = $.parseJSON(data);
                            } catch (e) {
                                console.log(e.message);
                            }

                            _this.submitDone(data);
                        } else {
                            _this.submitFail('Image upload failed!');
                        }

                        _this.submitEnd();

                    });
                });

                this.$iframe = $iframe;
                this.$avatarForm.attr('target', target).after($iframe.hide());
            },

            click: function() {
                this.$avatarModal.modal('show');
                this.initPreview();
            },

            change: function() {
                let files,
                    file;

                if (this.support.datauri) {
                    files = this.$avatarInput.prop('files');

                    if (files.length > 0) {
                        file = files[0];

                        if (this.isImageFile(file)) {
                            if (this.url) {
                                URL.revokeObjectURL(this.url); // Revoke the old one
                            }

                            this.url = URL.createObjectURL(file);
                            this.startCropper();
                        }
                    }
                } else {
                    file = this.$avatarInput.val();

                    if (this.isImageFile(file)) {
                        this.syncUpload();
                    }
                }
            },

            submit: function() {
                if (!this.$avatarSrc.val() && !this.$avatarInput.val()) {
                    return false;
                }

                if (this.support.formData) {
                    this.ajaxUpload();
                    return false;
                }
            },

            rotate: function(e) {
                let data;

                if (this.active) {
                    data = $(e.target).data();

                    if (data.method) {
                        this.$img.cropper(data.method, data.option);
                    }
                }
            },

            isImageFile: function(file) {
                if (file.type) {
                    return /^image\/\w+$/.test(file.type);
                } else {
                    return /\.(jpg|jpeg|png|gif)$/.test(file);
                }
            },

            startCropper: function() {
                let _this = this;

                if (this.active) {
                    this.$img.cropper('replace', this.url);
                } else {
                    this.$img = $('<img src="' + this.url + '">');
                    this.$avatarWrapper.empty().html(this.$img);
                    this.$img.cropper({
                        aspectRatio: 1,
                        preview: this.$avatarPreview.selector,
                        strict: false,
                        crop: function(data) {
                            let json = [
                                '{"x":' + data.x,
                                '"y":' + data.y,
                                '"height":' + data.height,
                                '"width":' + data.width,
                                '"rotate":' + data.rotate + '}'
                            ].join();

                            _this.$avatarData.val(json);
                        }
                    });

                    this.active = true;
                }
            },

            stopCropper: function() {
                if (this.active) {
                    this.$img.cropper('destroy');
                    this.$img.remove();
                    this.active = false;
                }
            },

            ajaxUpload: function() {
                let url = this.$avatarForm.attr('action'),
                    data = new FormData(this.$avatarForm[0]),
                    _this = this;

                $.ajax(apiUrl + 'uploadimg', {
                    type: 'post',
                    data: data,
                    dataType: 'json',
                    headers: {
                        'authorization': common.getStoreData('token')
                    },
                    processData: false,
                    contentType: false,

                    beforeSend: function() {
                        _this.submitStart();
                    },

                    success: function(data) {
                        _this.submitDone(data);
                    },

                    error: function(XMLHttpRequest, textStatus, errorThrown) {
                        _this.submitFail(textStatus || errorThrown);
                    },

                    complete: function() {
                        _this.submitEnd();
                    }
                });
            },

            syncUpload: function() {
                this.$avatarSave.click();
            },

            submitStart: function() {
                this.$loading.fadeIn();
            },

            submitDone: function(data) {
                if ($.isPlainObject(data) && data.errno === 0) {
                    if (data.info) {
                        this.url = data.info.uploadurl;
                        _self.avatar = data.info.uploadurl;

                        if (this.support.datauri || this.uploaded) {
                            this.uploaded = false;
                            this.cropDone();
                        } else {
                            this.uploaded = true;
                            this.$avatarSrc.val(this.url);
                            this.startCropper();
                        }

                        this.$avatarInput.val('');
                    } else if (data.message) {
                        this.alert(data.message);
                    }
                } else {
                    this.alert('Failed to response');
                }
            },

            submitFail: function(msg) {
                this.alert(msg);
            },

            submitEnd: function() {
                this.$loading.fadeOut();
            },

            cropDone: function() {
                this.$avatarForm.get(0).reset();
                this.$avatar.attr('src', this.url);
                this.stopCropper();
                this.$avatarModal.modal('hide');
            },

            alert: function(msg) {
                let $alert = [
                    '<div class="alert alert-danger avater-alert">',
                    '<button type="button" class="close" data-dismiss="alert">&times;</button>',
                    msg,
                    '</div>'
                ].join('');

                this.$avatarUpload.after($alert);
            }
        };

        $(function() {
            console.log('init success')
            common.reSizeCall()
            return new CropAvatar($('#crop-avatar'));
        })
    },
    methods: {
        clickDone: function() {
            let _self = this
            let param = {
                user_id: _self.employee.userDetail[0].user_id,
                username: $("#username").val(),
                phone: $("#phone").val(),
                idcarde_no: $("#idcarde_no").val(),
                // age: $("#age").val(),
                marital_status: common.getSelect2Val('marital_status'),
                education: common.getSelect2Val('education'),
                graduate_institution: $("#graduate_institution").val(),
                ethnicity: common.getSelect2Val('ethnicity'),
                birth_date: $("#birth_date").val(),
                graduate_date: $("#graduate_date").val(),
                native_place: $("#native_place").val(),
                living_place: $("#living_place").val(),
                register_category: common.getSelect2Val('register_category'),
                service_length: $("#service_length").val(),
                working_age: $("#working_age").val(),
                emergency_contact_person: $("#emergency_contact_person").val(),
                emergency_contact_phone: $("#emergency_contact_phone").val(),
                emergency_contact_qq: $("#emergency_contact_qq").val(),
                avatar: _self.avatar,
                info: '2'
            }

            _self.$http.post(apiUrl + 'modify', param).then((response)=>{
                $('#age').val(response.body.info.age);
                common.dealSuccessCommon('已提交');
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        addP: function(event) {
            let _self = this;
            _self.workRow = {};
            $('#goupload_format').val(null).trigger('change')
            $('#formEP').parsley().reset()
            _self.uploadImages = []
            _self.files = []
            $('#PersonalModal').modal('show')
        },
        saveForm: async function(event) {
            let _self = this;
            let formEP = $('#formEP')
            if (formEP.parsley().isValid()) {
                let _self = this;
                let param = {
                photo_title : '生活照',
                images : _self.files,
                goupload_format: common.getSelect2Val('goupload_format'),
                user_id: _self.employee.userDetail[0].user_id
                }

                try {
                    let response = await _self.$http.post(apiUrl + 'add_i', param);
                    let retData = response.data.info;
                    if (retData.photo_title) {
                        $('#addP').prop('disabled', true);
                    }
                    $('#personalTable').bootstrapTable("refresh", _self.queryParams);
                    formEP.parsley().reset();
                    $('#PersonalModal').modal('hide');
                } catch (error) {
                    common.dealErrorCommon(_self, error)
                }
            }
        },
        removImage: async function(index) {
            let _self = this
            _self.uploadImages.splice(index, 1)
            _self.files.splice(index, 1)
        }
    }
}
</script>
<style scoped>
#search_div input {
    width: 200px;
}
.avatar-view {
    display: block;
    height: 178px;
    width: 178px;
    border: 3px solid #fff;
    border-radius: 5px;
    box-shadow: 0 0 5px rgba(0, 0, 0, .15);
    cursor: pointer;
    overflow: hidden;
}

.avatar-view img {
    width: 100%;
}

.avatar-body {
    padding-right: 15px;
    padding-left: 15px;
}

.avatar-upload {
    overflow: hidden;
}

.avatar-upload label {
    display: block;
    float: left;
    clear: left;
    width: 100px;
}

.avatar-upload input {
    display: block;
    margin-left: 110px;
}

.avater-alert {
    margin-top: 10px;
    margin-bottom: 10px;
}

.avatar-wrapper {
    height: 364px;
    width: 100%;
    margin-top: 15px;
    box-shadow: inset 0 0 5px rgba(0, 0, 0, .25);
    background-color: #fcfcfc;
    overflow: hidden;
}

.avatar-wrapper img {
    display: block;
    height: auto;
    max-width: 100%;
}

.avatar-preview {
    float: left;
    margin-top: 15px;
    margin-right: 15px;
    border: 1px solid #eee;
    border-radius: 4px;
    background-color: #fff;
    overflow: hidden;
}

.avatar-preview:hover {
    border-color: #ccf;
    box-shadow: 0 0 5px rgba(0, 0, 0, .15);
}

.avatar-preview img {
    width: 100%;
}

.preview-lg {
    height: 184px;
    width: 184px;
    margin-top: 15px;
}

.preview-md {
    height: 100px;
    width: 100px;
}

.preview-sm {
    height: 50px;
    width: 50px;
}

@media (min-width: 992px) {
    .avatar-preview {
        float: none;
    }
}

.avatar-btns {
    margin-top: 30px;
    margin-bottom: 15px;
}

.avatar-btns .btn-group {
    margin-right: 5px;
}

.loading {
    display: none;
    position: absolute;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    background: #fff url("../../../../public/static/images/base/loading.gif") no-repeat center center;
    opacity: .75;
    filter: alpha(opacity=75);
    z-index: 20140628;
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
