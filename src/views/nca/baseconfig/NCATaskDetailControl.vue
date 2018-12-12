<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">任务管理</a></li>
            <li class="active">任务详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <div class="panel-heading">
                        <div class="panel-heading-btn">
                            <button v-show="(task.task_type == 3 || task.task_type == 5 || task.task_type == 6 ||
                            task.task_type == 15 || task.task_type == 16 || task.task_type == 18|| task.task_type == 19||
                            task.task_type == 20|| task.task_type == 21 || task.task_type == 23 || task.task_type == 26 ||
                            task.task_type == 31 || task.task_type == 44 || task.task_type == 45 || task.task_type == 46 ||
                            task.task_type == 58) && task.task_state == 1 && task.task_performer === userinfo.user_id"
                                    type="button" class="btn btn-info btn-xs" @click="clickDone">完成</button>
                            <button v-show="(task.task_type == 1 || task.task_type == 2 || task.task_type == 4 || task.task_type == 7 || task.task_type == 8 ||
                            task.task_type == 9 || task.task_type == 10 || task.task_type == 11 || task.task_type == 12 ||
                            task.task_type == 13 || task.task_type == 14 || task.task_type == 17  || task.task_type == 22  ||
                            task.task_type == 24 || task.task_type == 25 || task.task_type == 27 || task.task_type == 28 ||
                            task.task_type == 29 || task.task_type == 30 || task.task_type == 32 ||
                            task.task_type == 33 || task.task_type == 34 || task.task_type == 35 ||
                            task.task_type == 36 || task.task_type == 37 || task.task_type == 38 ||
                            task.task_type == 39 || task.task_type == 40 || task.task_type == 41 || task.task_type == 42 ||
                            task.task_type == 50 || task.task_type == 51 || task.task_type == 52 || task.task_type == 53 ||
                            task.task_type == 54 || task.task_type == 55 || task.task_type == 56 || task.task_type == 57 ||
                            task.task_type == 60 || task.task_type == 61 || task.task_type == 62 || task.task_type == 63 ||
                            task.task_type == 64 || task.task_type == 65 || task.task_type == 66)
                            && task.task_state == 1 && task.task_performer === userinfo.user_id"
                                    type="button" class="btn btn-info btn-xs" @click="clickReject">不同意</button>
                            <button v-show="(task.task_type == 1 || task.task_type == 2 || task.task_type == 4 || task.task_type == 7 || task.task_type == 8 ||
                            task.task_type == 9 || task.task_type == 10 || task.task_type == 11 || task.task_type == 12 ||
                            task.task_type == 13 || task.task_type == 14 || task.task_type == 17  || task.task_type == 22  ||
                            task.task_type == 24 || task.task_type == 25 || task.task_type == 27  || task.task_type == 28
                            || task.task_type == 29 || task.task_type == 30 || task.task_type == 32
                            || task.task_type == 33 || task.task_type == 34 || task.task_type == 35 || task.task_type == 36
                            || task.task_type == 37 || task.task_type == 38 || task.task_type == 39 || task.task_type == 40
                            || task.task_type == 41 || task.task_type == 42
                            || task.task_type == 50 || task.task_type == 51 || task.task_type == 52 || task.task_type == 53
                            || task.task_type == 54 || task.task_type == 55 || task.task_type == 56 || task.task_type == 57
                            || task.task_type == 60 || task.task_type == 61 || task.task_type == 62
                            || task.task_type == 63 || task.task_type == 64 || task.task_type == 65 || task.task_type == 66
                            || task.task_type == 70 || task.task_type == 71 || task.task_type == 72 || task.task_type == 73
                            ) && task.task_state == 1 && task.task_performer === userinfo.user_id"
                                    type="button" class="btn btn-info btn-xs" @click="clickReview">同意</button>
                            <button v-show="(task.task_type == 1 || task.task_type == 15 || task.task_type == 16) && task.task_state == 2 && task.task_publisher === userinfo.user_id"
                                    type="button" class="btn btn-info btn-xs" @click="clickConfirm">确认</button>
                            <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                        </div>
                        <h4 class="panel-title">任务详情</h4>
                    </div>
                    <div class="panel-body">
                        <div class="row">
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">任务单号</span>
                                    <input class="form-control" v-model="task.task_id" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6" v-show="task.task_type != 1">
                                <div class="input-group">
                                    <span v-if="task.task_type == 2 || task.task_type == 9 || task.task_type == 3
                                    || task.task_type == 4 || task.task_type == 5 || task.task_type == 6
                                    || task.task_type == 7 || task.task_type == 8 || task.task_type == 10
                                    || task.task_type == 11 || task.task_type == 12 || task.task_type == 13
                                    || task.task_type == 14 || task.task_type == 15 || task.task_type == 16
                                    || task.task_type == 17 || task.task_type == 18 || task.task_type == 19
                                    || task.task_type == 20 || task.task_type == 21 || task.task_type == 22
                                    || task.task_type == 23 || task.task_type == 24 || task.task_type == 25
                                    || task.task_type == 26 || task.task_type == 27 || task.task_type == 28
                                    || task.task_type == 29 || task.task_type == 30 || task.task_type == 31
                                    || task.task_type == 32 || task.task_type == 33 || task.task_type == 34
                                    || task.task_type == 35 || task.task_type == 36 || task.task_type == 37
                                    || task.task_type == 38 || task.task_type == 39 || task.task_type == 40
                                    || task.task_type == 41 || task.task_type == 42 || task.task_type == 43
                                    || task.task_type == 44 || task.task_type == 45 || task.task_type == 46
                                    || task.task_type == 50 || task.task_type == 51 || task.task_type == 52
                                    || task.task_type == 53 || task.task_type == 54 || task.task_type == 55
                                    || task.task_type == 56 || task.task_type == 57 || task.task_type == 58
                                    || task.task_type == 60 || task.task_type == 61
                                    || task.task_type == 62 || task.task_type == 63 || task.task_type == 64
                                    || task.task_type == 65 || task.task_type == 66
                                    || task.task_type == 70 || task.task_type == 71 || task.task_type == 72 || task.task_type == 73"
                                          class="input-group-addon">审核内容</span>
                                    <a class="form-control" v-if="task.task_type == 2" style="cursor: pointer" @click="goToPurchaseApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 9" style="cursor: pointer" @click="goToInvalidateApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 10" style="cursor: pointer" @click="goToStockApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 11" style="cursor: pointer" @click="goToStockOutApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 12" style="cursor: pointer" @click="goToProject(task)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 13" style="cursor: pointer" @click="goToProject(task)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 14" style="cursor: pointer" @click="goToNoticeApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 15" style="cursor: pointer" @click="goToHumanApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 16" style="cursor: pointer" @click="goToReturnApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 17" style="cursor: pointer" @click="goToIdleApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 22" style="cursor: pointer" @click="goToTransReceptionApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 24" style="cursor: pointer" @click="goToTrExpenseApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 25" style="cursor: pointer" @click="goToDocumentNoticeeApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 26" style="cursor: pointer" @click="goToDocumentOutNoticeeApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 27" style="cursor: pointer" @click="goToAskForLeaveApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 28" style="cursor: pointer" @click="goToSpecialExpenseApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 29" style="cursor: pointer" @click="goToAssetPurchaseApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 30" style="cursor: pointer" @click="goToAssetAcceptanceApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 31" style="cursor: pointer" @click="goToAssetMaintenanceApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 32" style="cursor: pointer" @click="goToAmortize(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 33" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 34" style="cursor: pointer" @click="goToAmortizeSubscribeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 35" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 36" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 37" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 38" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 42" style="cursor: pointer" @click="goToAmortizeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 39" style="cursor: pointer" @click="goToAssetRetirementApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 40 || task.task_type == 41" style="cursor: pointer" @click="goToConsumablesApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 43" style="cursor: pointer" @click="goToAssetInventoryApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 44" style="cursor: pointer" @click="goToAssetDoneInventoryApply(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 45" style="cursor: pointer" @click="goToGathering(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 46" style="cursor: pointer" @click="goToPaymentConfirm(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 50" style="cursor: pointer" @click="goToDevelop(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 51" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 52" style="cursor: pointer" @click="goToDevelopSubscribeDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 53" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 54" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 55" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 56" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 57" style="cursor: pointer" @click="goToDevelopDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 58" style="cursor: pointer" @click="goToReceiptDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <!--工程项目管理-->
                                    <a class="form-control" v-if="task.task_type == 60" style="cursor: pointer" @click="goToProjectDetailCopy60(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 61" style="cursor: pointer" @click="goToProjectDetailCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 62" style="cursor: pointer" @click="goToProjectDetailCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 63" style="cursor: pointer" @click="goToProjectDetailCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 64" style="cursor: pointer" @click="goToProjectDetailCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 65" style="cursor: pointer" @click="goToProjectCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <a class="form-control" v-if="task.task_type == 66" style="cursor: pointer" @click="goToProjectDetailCopy(task.task_review_code)">{{task.task_review_code}}</a>
                                    <!--工程项目管理end-->

                                    <a class="form-control" v-if="task.task_type > 69 && task.task_type < 74" style="cursor: pointer" @click="goToProductPlanVerifyDetail(task.task_review_code)">{{task.task_review_code}}</a>
                                    <span v-if="task.task_type == 7">
                                        <a class="form-control" v-show="task.task_state == 2" style="cursor: pointer" disabled>{{task.task_review_code}}</a>
                                        <a class="form-control" v-show="task.task_state == 1" style="cursor: pointer" @click="goToNCAMaterielReviewControl(task.task_review_code)">{{task.task_review_code}}</a>
                                    </span>
                                    <input class="form-control" v-if="task.task_type != 2 && task.task_type != 7 && task.task_type != 9
                                    && task.task_type != 10 && task.task_type != 11 && task.task_type != 12 && task.task_type != 13
                                    && task.task_type != 14 && task.task_type != 15 && task.task_type != 16 && task.task_type != 17
                                    && task.task_type != 22 && task.task_type != 24 && task.task_type != 25 && task.task_type != 26
                                    && task.task_type != 27 && task.task_type != 28 && task.task_type != 29 && task.task_type != 30
                                    && task.task_type != 31 && task.task_type != 32 && task.task_type != 33 && task.task_type != 34
                                    && task.task_type != 35 && task.task_type != 36 && task.task_type != 37 && task.task_type != 38
                                    && task.task_type != 39 && task.task_type != 40 && task.task_type != 41 && task.task_type != 42
                                    && task.task_type != 43 && task.task_type != 44 && task.task_type != 45 && task.task_type != 46
                                    && task.task_type != 50 && task.task_type != 51 && task.task_type != 52 && task.task_type != 53
                                    && task.task_type != 54 && task.task_type != 55 && task.task_type != 56 && task.task_type != 57
                                    && task.task_type != 58 && task.task_type != 60 && task.task_type != 61
                                    && task.task_type != 63 && task.task_type != 64 && task.task_type != 65 && task.task_type != 66
                                    && task.task_type != 70 && task.task_type != 71 && task.task_type != 72 && task.task_type != 73
                                    " v-model="task.task_review_code" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">任务名称</span>
                                    <input class="form-control" v-model="task.task_name" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">任务类型</span>
                                    <select class="form-control" v-model="task.task_type" disabled>
                                        <option value="">请选择任务类型</option>
                                        <option v-for="type in pagePara.taskTypeInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">优先级</span>
                                    <select class="form-control" v-model="task.task_priority" disabled>
                                        <option value="">请选择优先级</option>
                                        <option v-for="type in pagePara.taskPriorityInfo" v-bind:value="type.id">{{type.text}}</option>
                                    </select>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">创建时间</span>
                                    <input class="form-control" v-model="task.task_create_date" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6">
                                <div class="input-group">
                                    <span class="input-group-addon">已用时</span>
                                    <input class="form-control" v-model="task.task_expend_time" disabled>
                                </div>
                            </div>
                            <div class="form-group col-sm-6" v-show="task.task_type == 1">
                                <div class="input-group">
                                    <span class="input-group-addon">完成时间</span>
                                    <input class="form-control" v-model="task.end_time" disabled>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <div class="input-group">
                                    <span class="input-group-addon">任务描述</span>
                                    <textarea class="form-control" v-model="task.task_description" disabled></textarea>
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="form-group col-sm-12">
                                <div class="input-group">
                                    <span class="input-group-addon">任务说明</span>
                                    <textarea class="form-control" placeholder="请输入完成时需注明的信息" v-model="task.task_remark"
                                              id="task_remark" v-bind:disabled="task.task_state == 2 || task.task_performer !== userinfo.user_id">
                                    </textarea>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
  const common = require('commonFunc')
  const apiUrl = '/api/nca/baseconfig/NCATaskListControl?method=';

export default {
    data: function() {
        return {
            pagePara: '',
            task: '',
            userinfo: common.getStoreData('userinfo')
        }
    },
    name: 'NCATaskDetailControl',
    mounted: function() {
        let _self = this

        let taskId = _self.$route.query.taskId
        async function getInit() {
            try {
                let response = await _self.$http.post(apiUrl + 'init', {});
                _self.pagePara = response.data.info
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        async function getTaskDetail() {
            try {
                let param = {
                    task_id: taskId,
                    domain_id: _self.userinfo.domain_id
                }
                let response = await _self.$http.post(apiUrl + 'search', param);
                _self.task = response.data.info.rows[0]
                console.log(_self.task)

            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

        async function initPage() {
            await getInit()
            await getTaskDetail()
        }

        $(async function() {
            await initPage();
        });
    },
    methods: {
        clickDone: function() {
            let _self = this
            let param = {
                task_id: _self.task.task_id,
                task_remark: $('#task_remark').val()
            }
            _self.$http.post(apiUrl + 'complete', param).then((response)=>{
                common.dealSuccessCommon('任务已提交');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        clickConfirm: function() {
            let _self = this
            let param = {
                task_id: _self.task.task_id,
            }
            _self.$http.post(apiUrl + 'confirm', param).then((response)=>{
                common.dealSuccessCommon('任务已确认');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        goToPurchaseApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/purchasemanage/NCAPurchaseApplyDetailControl',
                query: {
                    purchaseapplyId:id,
                    show_return: 1
                }
            })
        },
        goToInvalidateApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/inventorymanage/NCAInvalidateApplyTaskControl',
                query: {
                    invalidateorderId: id,
                    show_return: 1
                }
            })
        },
        clickReject: function () {
            let _self = this
            let param = {
                task_id: _self.task.task_id,
                task_type: _self.task.task_type,
                review_result: 0,
                task_remark: $('#task_remark').val()
            }
            _self.$http.post(apiUrl + 'review', param).then((response)=>{
                common.dealSuccessCommon('申请已驳回');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        clickReview: function () {
            let _self = this
            let param = {
                task_id: _self.task.task_id,
                task_type: _self.task.task_type,
                review_result: 1,
                task_remark: $('#task_remark').val()
            }
            _self.$http.post(apiUrl + 'review', param).then((response)=>{
                common.dealSuccessCommon('申请已通过');
                window.history.back()
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });
        },
        goToNCAMaterielReviewControl: function (id) {
            let _self = this
//            if (task.task_state == 1){
            _self.$router.push({
                path: '/nca/baseconfig/NCAMaterielReviewDetailControl',
                query: {
                    review_materiel_code: id,
                    show_return: 1
                }
            })
//            }

        },
        goToStockApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/inventorymanage/NCAStockInApplyDetailControl',
                query: {
                    stockapplyId: id,
                    show_return: 1
                }
            })
        },
        goToStockOutApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/inventorymanage/NCAStockOutApplyDetailControl',
                query: {
                    stockoutapplyId: id,
                    show_return: 1
                }
            })
        },
        goToProject: function (task) {
            let _self = this
            if (task.task_type === '12') {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAProjectControl',
                    query: {
                    show_return: 1
                    }
                })
            } else {
                _self.$router.push({
                    path: '/nca/baseconfig/NCAFinalAccountControl',
                    query: {
                    show_return: 1
                    }
                })
            }

        },
        goToNoticeApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCANoticeDetailControl',
                query: {
                    notice_id: id,
                    show_return: 1
                }
            })
        },
        goToHumanApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCAHumanResourceDetailControl',
                query: {
                    hr_id: id,
                    show_return: 1
                }
            })
        },
        goToReturnApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/purchasemanage/NCAReturnNoteDetailControl',
                query: {
                    return_id: id,
                    show_return: 1
                }
            })
        },
        goToTransReceptionApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCATransReceptionDetailControl',
                query: {
                    trapplyCode: id,
                    show_return: 1
                }
            })
        },
        goToTrExpenseApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCATransReceptionExpenseDetailControl',
                query: {
                    trExpenseCode: id,
                    show_return: 1
                }
            })
        },
        goToIdleApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/inventorymanage/NCAIdleApplyDetailControl',
                query: {
                    idleapply_id: id,
                    show_return: 1
                }
            })
        },
        goToSpecialExpenseApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCASpecialExpenseControl',
                query: {
                    sExpenseCode: id,
                    show_return: 1
                }
            })
        },
        goToDocumentNoticeeApply: function (id) {
            let _self = this
            let  tt = (id).search("N") == 0
            if (tt) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCADocumentManagementTaskDetailControl',
                    query: {
                        document_id: id,
                        show_return: 1,
                        show_answer: 0
                    }
                })
            } else {
                _self.$router.push({
                    path: '/nca/baseconfig/NCATestTaskOutDetailControl',
                    query: {
                        document_id: id,
                        show_return: 1,
                        show_answer: 0
                    }
                })
            }
        },
        goToDocumentOutNoticeeApply: function (id) {
            let _self = this
            let  tt = (id).search("N") == 0
            if (tt) {
                _self.$router.push({
                    path: '/nca/baseconfig/NCADocumentManagementNoticeTaskDetailControl',
                    query: {
                        document_id: id,
                        show_return: 1,
                        show_answer: 1
                    }
                })
            } else {
                _self.$router.push({
                    path: '/nca/baseconfig/NCATestTaskOutDetailControl',
                    query: {
                        document_id: id,
                        show_return: 1,
                        show_answer: 1
                    }
                })
            }
        },
        goToAskForLeaveApply:function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCAAskForLeaveTaskDetailControl',
                query: {
                    askforleave_id: id,
                    show_return: 1
                }
            })
        },
        goToAssetPurchaseApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAAssetPurchaseTaskDetailControl',
                query: {
                    fixedassetspurch_id: id,
                    show_return: 1
                }
            })
        },
        goToConsumablesApply : function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAConsumablesDetailControl',
                query: {
                    consumablesCode: id,
                    show_return: 1
                }
            })
        },
        goToAssetAcceptanceApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAAssetsAcceptanceTaskDetailControl',
                query: {
                    fixedassetscheck_id: id,
                    show_return: 1
                }
            })
        },
        goToAssetRetirementApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAFixedAssetsScrapTaskDetailControl',
                query: {
                    longassetsscrap_id: id,
                    show_return: 1
                }
            })
        },
        goToAmortize: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAAmortizeControlCopy',
                query: {
                    amortize_id: id,
                    show_return: 1
                }
            })
        },
        goToAmortizeDetail: function (id) {
            let _self = this
            console.log(_self.task,'_self.task')
            _self.$router.push({
                path: '/nca/longtermassets/NCAAmortizeDetailControlCopy',
                query: {
                    amortize_id: id,
                    show_return: 1
                }
            })
        },
        goToAmortizeSubscribeDetail: function (id) {
            let _self = this
            console.log(_self.task,'_self.task')
            _self.$router.push({
                path: '/nca/longtermassets/NCAAmortizeScribeOrderDetailControlCopy',
                query: {
                    amortizesubscribeorder_id: id,
                    show_return: 1
                }
            })
        },
        // goToAmortizeConsumeDetail: function (id) {
        //     let _self = this
        //     console.log(_self.task,'_self.task')
        //     _self.$router.push({
        //         path: '/nca/longtermassets/NCAAmortizeConsumeControlCopy',
        //         query: {
        //             amortizeconsume_id: id,
        //             show_return: 1
        //         }
        //     })
        // },
        goToDevelop: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCADevelopControlCopy',
                query: {
                    develop_id: id,
                    show_return: 1
                }
            })
        },
        goToDevelopDetail: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCADevelopDetailControlCopy',
                query: {
                    develop_id: id,
                    show_return: 1
                }
            })
        },
        goToDevelopSubscribeDetail: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCADevelopScribeOrderDetailControlCopy',
                query: {
                    developsubscribeorder_id: id,
                    show_return: 1
                }
            })
        },
        // goToDevelopConsumeDetail: function (id) {
        //     let _self = this
        //     console.log(_self.task,'_self.task')
        //     _self.$router.push({
        //         path: '/nca/longtermassets/NCAAmortizeConsumeControlCopy',
        //         query: {
        //             developconsume_id: id,
        //             show_return: 1
        //         }
        //     })
        // },
        goToAssetMaintenanceApply: function (id) {
            let _self = this
            let  tt = (id).search("N") == 0
            console.log('tt',tt)
            if (!tt) {
                _self.$router.push({
                    path: '/nca/longtermassets/NCAFixedAssetsRepairMaterials',
                    query: {
                        fixedassetsrepair_id: id,
                        show_return: 1
                    }
                })
            }
        },
        goToAssetInventoryApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAAssetInventoryTaskDetailControl',
                query: {
                    take_stock_no: id,
                    taskId:_self.$route.query.taskId,
                    show_return: 1
                }
            })
        },
        goToAssetDoneInventoryApply: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/longtermassets/NCAAssetInventoryDetailControl',
                query: {
                    take_stock_no: id,
                    show_return: 1
                }
            })
        },
        goToGathering: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/cashiermanage/NCAGatheringTempControl',
                query: {
                    cashiergathering_id: id,
                    show_return: 1
                }
            })
        },
        goToPaymentConfirm: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/cashiermanage/NCAPaymentConfirmControl',
                query: {
                    paymentconfirm_id: id,
                    show_return: 1
                }
            })
        },
        goToReceiptDetail: function (id) {
          let _self = this
          _self.$router.push({
            path: '/nca/inventorymanage/NCAReceiptDetailControl',
            query: {
              receipt_id: id,
            }
          })
        },
        goToProductPlanVerifyDetail(id) {
            this.$router.push({
                path: '/nca/productionmanage/NCAPlanVerifyListControl',
                query: {
                    productId: id,
                }
            })
        },
        goToProjectCopy: function (id) {
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCAProjectControlCopy',
                query: {
                    project_id: id
                }
            })
        },
        goToProjectDetailCopy: function (id) {
            //61,62,63,64,65,66
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCAProjectDetailNewControlCopy',
                query: {
                    projectId: id
                }
            })
        },
        goToProjectDetailCopy60: function (id) {
            //60
            let _self = this
            _self.$router.push({
                path: '/nca/baseconfig/NCAProjectDetailNewControlCopy',
                query: {
                    projectsubscribeorder_id: id,
                }
            })
        },
    }
}
</script>
<style scoped>
</style>
