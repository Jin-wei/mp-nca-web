<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">投诉管理</a></li>
            <li class="active">客户投诉列表</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <DomainChooseHeader :pagePara="pagePara" :apiName="apiName" :listTitle="'客户投诉列表'"></DomainChooseHeader>
                    <div class="panel-toolbar">
                        <div class="form-inline" role="form">
                            <div class="form-group">
                                <input class="form-control" id="search_complaint" placeholder="搜索订单号、客户姓名"
                                       style="width: 200px;">
                            </div>
                            <div class="form-group">
                                <button class="btn btn-info" v-on:click="searchFeedback(feedbackIndex)"><i
                                        class="fa fa-search"></i></button>
                            </div>
                            <div class="form-group pull-right">
                                <button id="addM" class="btn btn-info" v-on:click="showCreateModal">增加
                                </button>
                            </div>
                        </div>
                    </div>
                    <div class="panel-body auto-height">
                        <ul class="nav nav-tabs">
                            <li class="active" @click="searchFeedback(0)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">客户投诉</span>
                                    <span class="hidden-xs">客户投诉</span>
                                </a>
                            </li>
                            <li @click="searchFeedback(1)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">客户报修</span>
                                    <span class="hidden-xs">客户报修</span>
                                </a>
                            </li>
                            <li @click="searchFeedback(2)">
                                <a href="#tabletab" data-toggle="tab">
                                    <span class="visible-xs">客户反馈</span>
                                    <span class="hidden-xs">客户反馈</span>
                                </a>
                            </li>
                        </ul>
                        <div class="tab-content">
                            <form class="form-horizontal radio-container" @change="onStatusChanged">
                                <div class="radio radio-inline radio-css radio-success">
                                    <input type="radio" name="cssRadio" id="fbType1" value="0"
                                           v-model="feedbackStatus"/>
                                    <label for="fbType1">
                                        全部
                                    </label>
                                </div>
                                <div class="radio radio-inline radio-css radio-success">
                                    <input type="radio" name="cssRadio" id="fbType2" value="2"
                                           v-model="feedbackStatus"/>
                                    <label for="fbType2">
                                        已受理
                                    </label>
                                </div>
                                <div class="radio radio-inline radio-css radio-success">
                                    <input type="radio" name="cssRadio" id="fbType3" value="1"
                                           v-model="feedbackStatus"/>
                                    <label for="fbType3">
                                        未受理
                                    </label>
                                </div>
                            </form>

                            <div class="tab-pane fade active in" id="tabletab">
                                <table id="complaintTable"></table>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="complaintModal" data-backdrop="static">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">新增客户投诉列表</h4>
                    </div>
                    <form @submit.prevent="createComplaint" id="formComplaint">
                        <div class="modal-body row">
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户姓名</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="postFeedback.user_id"
                                           data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户邮箱</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="postFeedback.email" data-parsley-type="email"
                                           data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>客户QQ</label>
                                <div class="col-sm-8">
                                    <input class="form-control" v-model="postFeedback.qq_no" data-parsley-type="number"
                                           data-parsley-required="true">
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <label class="col-sm-4 control-label"><span class="table-required">*</span>受理人</label>
                                <div class="col-sm-8">
                                    <select class="form-control select2" multiple style="width:100%"
                                            id="complaint_responser" data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12" style="padding-left: 5px">
                                <label class="col-sm-2 control-label"><span class="table-required">*</span>类型</label>
                                <div class="col-sm-10">
                                    <select class="form-control select2" multiple style="width:100%" id="feedback_type"
                                            data-parsley-required="true"></select>
                                </div>
                            </div>
                            <div class="form-group col-sm-12" style="padding-left: 5px">
                                <label class="col-sm-2 control-label"><span class="table-required">*</span>投诉内容</label>
                                <div class="col-sm-10">
                                    <textarea class="form-control" v-model="postFeedback.content"
                                              data-parsley-required="true" maxlength="200"
                                              data-parsley-maxlength="200"></textarea>
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
        <div class="modal fade" id="handleModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">受理投诉</h4>
                    </div>
                    <form @submit.prevent="handleFeedback" id="formHandle">
                        <div class="modal-body row">
                            <div class="form-group col-sm-12">
                                <label class="col-md-2 control-label">处置记录</label>
                                <div class="col-md-10">
                                        <textarea class="form-control" rows="5"
                                                  id="complaint_handle_record"
                                                  data-parsley-required="true"
                                                  maxlength="300"
                                                  data-parsley-maxlength="300" v-model="workRow.record_content">
                                        </textarea>
                                </div>
                            </div>

                            <div class="form-group col-sm-12 no-padding" v-if="feedbackIndex === 1">
                                <div class="form-group col-sm-12 no-padding">
                                    <label class="col-md-2 control-label">负责人姓名</label>
                                    <div class="col-md-10">
                                        <input class="form-control" v-model="workRow.resp_person"
                                               data-parsley-required="true" maxlength="50" data-parsley-maxlength="50">
                                    </div>
                                </div>

                                <div class="form-group col-sm-12 no-padding">
                                    <label class="col-md-2 control-label">负责人电话</label>
                                    <div class="col-md-10">
                                        <input class="form-control" v-model="workRow.resp_phone"
                                               data-parsley-type="number"
                                               data-parsley-required="true">
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

        <div class="modal fade" id="detailModal">
            <div class="modal-dialog modal-lg">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">详情</h4>
                    </div>
                    <div class="modal-body">
                        <h5>{{btnTitle[feedbackIndex]}}内容：</h5>
                        <p>{{viewRow.content}}</p>
                        <div>时间：<span>{{viewRow.created_at|datetime}}</span></div>

                        <div v-if="viewRow.images">
                            <h5>图片：</h5>
                            <img width="800" :src="viewRow.images"/>
                        </div>

                        <br/>
                        <h5>结果</h5>
                        <p>{{viewRow.record_content}}</p>
                        <div>受理时间：<span>{{viewRow.updated_at|datetime}}</span></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
    const common = require('commonFunc')
    const apiUrl = '/api/nca/customermanage/NCAComplaintControl?method='

    import DomainChooseHeader from '../../../components/common/DomainChooseHeader.vue'

    export default {
        data: function () {
            return {
                btnTitle: ['投诉', '报修', '反馈'],
                pagePara: '',
                workRow: {},
                viewRow: {},
                postFeedback: {},
                apiName: common.getApiName(apiUrl),
                mainTable: null,
                feedbackIndex: 0,
                feedbackStatus: 0,
                listTitle:''
            }
        },
        name: 'NCAComplaintControl',
        components: {
            DomainChooseHeader
        },
        filters: {
            datetime(value) {
                const date = new Date(value);
                return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}`;
            }
        },
        mounted: function () {
            this.mainTable = $('#complaintTable');
            this.initPage();
        },
        methods: {
            searchFeedback(index) {
                this.feedbackIndex = index;
                if (this.feedbackIndex === 1) {
                    this.mainTable.bootstrapTable('showColumn', 'repair_type');
                    this.mainTable.bootstrapTable('showColumn', 'resp_person');
                    this.mainTable.bootstrapTable('showColumn', 'resp_phone');
                } else {
                    this.mainTable.bootstrapTable('hideColumn', 'repair_type');
                    this.mainTable.bootstrapTable('hideColumn', 'resp_person');
                    this.mainTable.bootstrapTable('hideColumn', 'resp_phone');
                }
                this.mainTable.bootstrapTable("refresh")
            },
            onStatusChanged(event) {
                console.log('onStatusChanged:', event);
                this.mainTable.bootstrapTable("refresh")
            },
            showCreateModal(event) {
                $('#formComplaint').parsley().reset()
                $('#complaint_responser').val(null).trigger('change');
                $('#complaintModal').modal('show')
            },
            async createComplaint() {
                console.log('createComplaint:', this.postFeedback);
                if ($('#formComplaint').parsley().isValid()) {
                    this.postFeedback.type = common.getSelect2Val('feedback_type')
                    this.postFeedback.operator_id = common.getSelect2Val('complaint_responser')

                    try {
                        await this.$http.post(apiUrl + 'createUserFeedback', this.postFeedback);
                        this.postFeedback = {};
                        $('#complaintModal').modal('hide');
                        this.mainTable.bootstrapTable("refresh");
                        common.dealSuccessCommon('增加成功')
                    } catch (error) {
                        common.dealErrorCommon(this, error)
                    }
                }
            },
            async handleFeedback() {
                if ($('#formHandle').parsley().isValid()) {
                    try {
                        const {user_id} = common.getStoreData('userinfo');
                        const result = await this.$http.post(apiUrl + 'updateUserFeedback', {
                            feedback_id: this.workRow.feedback_id,
                            record_content: this.workRow.record_content,
                            resp_person: this.workRow.resp_person,
                            resp_phone: this.workRow.resp_phone,
                            operator_id: user_id,
                            status: 2
                        });

                        this.workRow = {};
                        $('#handleModal').modal('hide');

                        if (result.errno < 0) {
                            common.dealErrorCommon(this, result.msg);
                        } else {
                            this.mainTable.bootstrapTable("refresh");
                            common.dealSuccessCommon('已受理')
                        }
                    } catch (error) {
                        this.workRow = {};
                        $('#handleModal').modal('hide');
                        common.dealErrorCommon(this, error)
                    }
                }
            },
            async getInit() {
                try {
                    let response = await this.$http.post(apiUrl + 'initUserFeedback', {});
                    this.pagePara = response.data.info
                    common.initSelect2Editable($('#feedback_type'), this.pagePara.feedbackInfo);
                    common.initSelect2Editable($('#complaint_responser'), this.pagePara.staffInfo);
                } catch (error) {
                    common.dealErrorCommon(this, error)
                }
            },
            initTable() {
                window.tableEvents = {
                    'click .show_customer_detail': (e, value, row, index) => {
                        let url = '/nca/customermanage/NCACustomerDetailControl?userId=' + row.user_id + '&offset=' + 0;
                        this.$router.push({
                            path: url
                        })
                    },
                    'click .handle_complaint': (e, value, row, index) => {
                        this.workRow = row;
                        $('#handleModal').modal('show');
                        $('#formHandle').parsley().reset()
                    },
                    'click .view_complaint': (e, value, row, index) => {
                        this.viewRow = row;
                        $('#detailModal').modal('show');
                    }
                };

                this.mainTable.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'getUserFeedback',
                    queryParams: (params) => {
                        let domain_id = common.getSelect2Val('domain_select');
                        if (domain_id) {
                            params.domain_id = domain_id
                        }
                        params.type = this.feedbackIndex + 1;
                        params.status = this.feedbackStatus;
                        return JSON.stringify(params)
                    },
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: (res) => {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatWithFEL('name', '客户姓名', (value, row) => {
                            return `<a class="show_customer_detail" style="cursor: pointer;">${value}</a>`
                        }, tableEvents),
                        common.BTRowFormat('phone', '客户手机号'),
                        // common.BTRowFormat('content', '投诉内容'),
                        common.BTRowFormatAlignLeft('email', '客户邮箱'),
                        common.BTRowFormatAlignLeft('qq_no', '客户QQ'),
                        common.BTRowFormatWithFW('created_at', '提交时间', value => {
                            const date = new Date(value);
                            return `${date.getFullYear()}-${date.getMonth() + 1}-${date.getDate()} ${date.getHours()}:${date.getMinutes()}`;
                        }),
                        common.BTRowFormatEdSelect2Disabled(this, 'status', '状态', 'feedbackState'),
                        common.BTRowFormatEdSelect2Disabled(this, 'repair_type', '问题类型', 'repairInfo'),
                        common.BTRowFormatEdSelect2DisabledAlignLeft(this, 'operator_id', '受理人', 'staffInfo'),
                        common.BTRowFormatAlignLeft('resp_person', '负责人'),
                        common.BTRowFormat('resp_phone', '联系电话'),
                        // common.BTRowFormatWrap('record_content', '处置记录'),
                        common.actFormatter('handle', (value, row) => {
                            if (row.status < 2) {
                                return `<a class="btn btn-info btn-xs m-r-5 handle_complaint">受理${this.btnTitle[this.feedbackIndex]}</a><a class="btn btn-info btn-xs m-r-5 view_complaint">查看</a>`;
                            } else {
                                return `<a class="btn btn-info btn-xs m-r-5 view_complaint">查看</a>`;
                            }
                        }, tableEvents)
                    ],
                    sortOrder: 'asc',
                    idField: 'feedback_id',
                    uniqueId: 'feedback_id',
                    striped: true,
                    clickToSelect: true,
                    pagination: true,
                    pageSize:common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row)
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modify', row, 'complaint_id', this.mainTable)
                    }
                });
                common.changeTableClass(this.mainTable);
                this.mainTable.bootstrapTable('hideColumn', 'repair_type');
                this.mainTable.bootstrapTable('hideColumn', 'resp_person');
                this.mainTable.bootstrapTable('hideColumn', 'resp_phone');
            },
            async initPage() {
                await this.getInit();
                $('#domain_select').on('change', (evt) => {
                    this.searchFeedback(this.feedbackIndex)
                });
                this.initTable();
                $('#formComplaint').parsley();
                $('#formHandle').parsley();
            }
        }
    }
</script>
<style scoped>
    .radio-container {
        margin-bottom: 10px;
    }
</style>
