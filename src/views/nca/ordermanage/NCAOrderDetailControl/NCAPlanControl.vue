<template>
<div class="panel panel-inverse">
    <div v-show="!checkState('SIGNED')">
        等待订单签约完成
    </div>
    <div class="panel-body" v-show="checkState('SIGNED')">
        <div>
            <div class="form-inline" role="form">
                <div class="form-inline div-style">
                    <div class="form-group">
                        <span>预计开工日期：</span>
                        <input class="form-control" id="start_date" readonly placeholder="选择开工日期">
                    </div>
                    <div class="form-group">
                        <span>实际开工日期：</span>
                        <input class="form-control" id="actual_start_date" placeholder="选择实际开工日期">
                    </div>
                    <div class="form-group">
                        <button class="btn btn-info" v-on:click="saveOrder">
                            保存日期
                        </button>
                    </div>
                    <!--<div class="form-group">-->
                        <!--<input type="checkbox" id="sat_off_id">周六-->
                        <!--<input type="checkbox" id="sun_off_id">周日-->
                    <!--</div>-->
                    <!--<div class="form-group" style="margin-right: 0px;">-->
                        <!--<span>施工节点模版：</span>-->
                    <!--</div>-->
                    <!--<div class="form-group" style="width:160px;">-->
                        <!--<select class="form-control select2" multiple id="search_templete_id"></select>-->
                    <!--</div>-->
                    <div class="form-group">
                        工期：<span id="schedule_id">{{orderDuration}}</span>天
                    </div>
                    <div v-show="!checkState('WORKING')" class="form-group pull-right" style="margin-right: 0px">
                        <button class="btn btn-info" v-on:click="generatePlan">
                            生成计划
                            </button>
                    </div>
                </div>
            </div>
        </div>
        <div>
            <div id="gantt_here" style='width:100%;'></div>
        </div>

        <!--<div>-->
            <!--<div class="panel panel-inverse m-t-10">-->
                <!--<div class="panel-heading">-->
                    <!--<div class="panel-heading-btn">-->
                        <!--<a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>-->
                    <!--</div>-->
                    <!--<h4 class="panel-title">修改记录</h4>-->
                <!--</div>-->
                <!--<div class="panel-body" style="height: 150px;">-->
                    <!--<table id="modifyLogTable"></table>-->
                <!--</div>-->
            <!--</div>-->
        <!--</div>-->

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">添加责任人</h4>
                    </div>
                    <div class="modal-body row">
                        <div class="form-group col-sm-12">
                            <label class="col-sm-3 control-label">责任人</label>
                            <div class="col-sm-9">
                                <select class="form-control select2" style="width:100%" id="leader_id"> </select>
                            </div>
                        </div>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-info" v-on:click="modifyAct" id="addAct">确认保存</button>
                    </div>
                </div>
            </div>
        </div>
        <div class="modal fade" id="leaderInfoModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">责任人信息</h4>
                    </div>
                    <div class="modal-body row">
                        <div class="form-group col-sm-12">
                            <label class="col-sm-3 control-label" id="leader_info_id"></label>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="ganttLogModal">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true" v-on:click="searchAct">×</button>
                    <h4 class="modal-title">修改原因</h4>
                </div>
                <div class="modal-body row">
                    <div class="form-group col-sm-12">
                        <label class="col-sm-3 control-label">修改原因</label>
                        <div class="col-sm-9">
                            <select class="form-control select2" style="width:100%" id="update_reason"> </select>
                        </div>
                    </div>
                    <div class="form-group col-sm-12">
                        <label class="col-sm-3 control-label">备注</label>
                        <div class="col-sm-9">
                            <textarea class="form-control select2" style="width:100%" id="gantt_remark"> </textarea>
                        </div>
                    </div>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-info" v-on:click="modifyGanttInfo">确认保存</button>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddModalImg" data-backdrop="static">
        <div class="modal-dialog">
            <div class="modal-content">

                <div class="modal-header build-heard">
                    <span id="logTitle"></span>
                    <span class="closeModal" data-dismiss="modal">x</span>
                </div>

                <div class="modal-body build-Main">
                    <div v-if="items.fWorklog !=null && items.fWorklog.length>0">
                        <div class="build-info1">
                            <img v-show="items.fWorklog[0].avatar != ''" :src="items.fWorklog[0].avatar" alt="">
                            <span>工长 &nbsp;&nbsp; {{items.fWorklog[0].name}}</span>
                            <span>{{items.fWorklog[0].created_at}}</span>
                        </div>

                        <div style="margin-top: 10px">
                            <span>
                                    {{items.fWorklog[0].content}}
                                </span>
                        </div>

                        <div class="build-pictureMain">
                            <div class="build-thumbnailMain">

                                <div v-for="file in items.fFiles" class="bulid-thumbnail">
                                    <img :src="file.file_url" alt="" v-on:click="showImg(file.file_url,2)">
                                </div>
                            </div>
                            <div class="bulid-bigPicture">
                                <img src="../../../../../public/static/images/base/head.gif" id="fBigPic" alt="">

                            </div>
                        </div>
                    </div>

                    <div v-if="items.sWorklog !=null && items.sWorklog.length>0">
                        <div class="build-info1">
                            <img v-show="items.sWorklog[0].avatar != ''" :src="items.sWorklog[0].avatar" alt="">
                            <span>监理 &nbsp;&nbsp; {{items.sWorklog[0].name}}</span>
                            <span>{{items.sWorklog[0].created_at}}</span>
                        </div>

                        <div style="margin-top: 10px">
                            <span>
                                {{items.sWorklog[0].content}}
                            </span>
                        </div>

                        <div class="build-pictureMain">
                            <div class="build-thumbnailMain">
                                <div v-for="file in items.sFiles" class="bulid-thumbnail">
                                    <img :src="file.file_url" alt="" v-on:click="showImg(file.file_url,1)">
                                </div>
                            </div>

                            <div class="bulid-bigPicture">
                                <img src="../../../../../public/static/images/base/head.gif" id="sBigPic" alt="">

                            </div>
                        </div>
                    </div>

                    <div v-if="items.comment !=null && items.comment.length>0">
                        <h5>评论</h5>
                        <p>{{items.comment[0].content}}</p>
                        <div align="right">
                            <span>{{items.comment[0].from_name}}</span>
                            <span>{{items.comment[0].created_at}}</span>
                        </div>
                        <div v-if="items.comment.length>1">
                            <div>
                                <p>回复:{{items.comment[1].content}}</p>
                                <div align="right">
                                    <span>{{items.comment[1].from_name}}</span>
                                    <span>{{items.comment[1].created_at}}</span>
                                </div>
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
const apiUrl = '/api/nca/ordermanage/NCAOrderDetailControl?method='

var curTaskId = "";
var curTask; //甘特图中当前选中记录
var ifExist = 0; //是否已生成甘特图，0：没有；1：已生成

export default {
    props: ['order'],
    data: function() {
        return {
            pagePara: {},
            workRow: {},
            oldRow: '',
            items: [],
            host: common.getUrl(),
            orderDuration:0
        }
    },
    name: 'NCAPlanControl',

    mounted: function() {
        let _self = this

        $(function() {
            //                initPage()
        })
    },
    watch: {
        order: function() {
            let _self = this;
            let templateList = null;
            let $modifyLogTable = $('#modifyLogTable');

            function initPageWatch() {
                var params = new Object();
                params.order_id = _self.order.order_id;
                params.domain_id = common.getStoreData('userinfo').domain_id;
                _self.$http.post(apiUrl + 'workplan_init', params).then((response) => {
                    let retData = response.data.info
                    _self.pagePara = JSON.parse(JSON.stringify(retData))
                    templateList = retData.templateList;
                    common.initSelect2($('#search_templete_id'), retData.templateList);
                    $("#search_templete_id").val(retData.construction_template_id).trigger("change");
                    common.initSelect2($('#update_reason'), retData.updateReasonList);
                    $("#start_date").val(retData.orderInfo[0].break_date);
                    $("#actual_start_date").val(retData.orderInfo[0].actual_start_date);
                    common.initDatepicker($('#actual_start_date'));
                    initModifyLogTable()
                    getModifyLogData()
                    common.reSizeCall()
                    console.log('init success')
                }, (response) => {
                    console.log('init error')
                    common.dealErrorCommon(_self, response)
                })
            }

            function getWorkLog(taskId) {
                let params = {};
                params.order_id = _self.order.order_id;
                params.gantttasks_id = taskId;
                params.host = _self.host;

                _self.$http.post(apiUrl + 'workplan_search_w', params).then((response) => {
                    _self.items = response.data.info;
                    console.log(_self.items)
                    $("#logTitle").html(_self.items.task.text + '-节点详情');
                    $('#AddModalImg').modal('show');
                    window.setTimeout(function() {
                        if (response.data.info.sFiles != null && response.data.info.sFiles.length > 0) {
                            //监理大图
                            $("#sBigPic").attr('src', response.data.info.sFiles[0].file_url);

                        }
                        if (response.data.info.fFiles != null && response.data.info.fFiles[0].file_url != null) {
                            //工长大图
                            $("#fBigPic").attr('src', response.data.info.fFiles[0].file_url);
                        }
                    }, 100)

                }, (response) => {
                    console.log('search error')
                    common.dealErrorCommon(_self, response)
                });

            }

            function getGantte() {

                var params = new Object();
                params.order_id = _self.order.order_id;
                _self.$http.post(apiUrl + 'workplan_search', params).then((response) => {
                    let retData = response.data.info
                    let dataArr = [];
                    if (retData != null && retData.length > 0) {
                        ifExist = 1;
                        _self.orderDuration = 0;
                        for (var i = 0; i < retData.length; i++) {
                            _self.orderDuration += retData[i].duration;
                            let obj = new Object();
                            obj.id = retData[i].gantttasks_id;
                            obj.start_date = retData[i].start_date;
                            obj.text = retData[i].text;
                            obj.duration = retData[i].duration;
                            obj.progress = retData[i].progress;
                            obj.progressColor = "#0fd16f";
                            obj.leader_id = retData[i].leader_id;
                            obj.leader_name = retData[i].leader_name;
                            obj.phone = retData[i].phone;
                            obj.open = true;
                            dataArr.push(obj)
                        }
                    }
                    var tasks = {
                        data: dataArr,
                        links: [
                            //                                { id:1, source:1, target:2, type:"1"}
                        ]
                    };

                    //甘特图双击不显示详情
                    gantt.config.details_on_dblclick = false;
                    gantt.config.readonly = true;
                    //不显示link节点
                    gantt.config.drag_links = false;
                    gantt.config.show_progress = true;
                    gantt.config.date_scale = "%M %d";

                    gantt.config.columns = [{
                            name: "text",
                            label: "施工节点",
                            tree: true,
                            width: 110
                        },
                        {
                            name: "",
                            label: "操作",
                            align: "center",
                            template: function(obj) {
                                if (obj.leader_id == null || obj.leader_id == "") return '待分配'
                                else return '<div><a class="addLeader" style="cursor: pointer;">' + obj.leader_name + '</a>' +
                                    '<input style="display: none" id="leaderId" value="' + obj.leader_name + '|' + obj.phone + '"/>' +
                                    '</div>'
                            }
                        }
                    ];

                    $("#gantt_here").height(common.getTableHeight() - 240);
                    gantt.init("gantt_here");
                    $(".gantt_grid_head_add").css("display", "none");
                    gantt.clearAll();
                    window.setTimeout(function() {
                        gantt.parse(tasks);
                        $(".gantt_grid_head_add").css("display", "none");

                        $("#gantt_here").on('click', '.addLeader', function() {
                            let curLeaderInfo = $(this).parent().find("#leaderId").val();
                            $("#leader_info_id").html(curLeaderInfo);
                            curTask = $(this).parent();
                            $('#leaderInfoModal').modal('show')
                        });

                        $("#gantt_here").on('click', '.showLog', function() {
                            getWorkLog($(this).find(".logTaskId").html());
                        });

                    }, 1000);

                    common.reSizeCall()
                    console.log('search success')
                }, (response) => {
                    console.log('search error')
                    common.dealErrorCommon(_self, response)
                })
            }

            gantt.attachEvent("onAfterTaskDrag", function(id, mode) {
                var task = gantt.getTask(id);
                curTask = gantt.getTask(id);
                var params = {};
                params.gantttasks_id = task.id;

                if (mode == gantt.config.drag_mode.progress) {
                    //任务进度变化
                    var pr = Math.floor(task.progress * 10) / 10;
                    params.progress = pr;

                    _self.$http.post(apiUrl + 'workplan_modify', params).then((response) => {

                        console.log('modify success')
                    }, (response) => {
                        console.log('modify error')
                        common.dealErrorCommon(_self, response)
                    })
                } else {
                    //任务时间变化
                    params.start_date = task.start_date;
                    params.duration = task.duration;

                    $("#gantt_remark").val("");
                    $("#update_reason").val(null).trigger('change');
                    if (_self.checkState('WORKING')) {
                        $('#ganttLogModal').modal('show')
                    } else {
                        _self.modifyGanttInfo()
                    }
                }
            });

            $('#search_templete_id').on('change', function() {
                if ($('#search_templete_id').val() != "") {
                    if (templateList != null && templateList.length > 0) {
                        for (var i = 0; i < templateList.length; i++) {
                            if ($('#search_templete_id').val() == templateList[i].id) {
                                $('#schedule_id').html(templateList[i].schedule);
                                break;
                            }
                        }
                    }
                }

            });

            function getModifyLogData() {
                let params = {};
                params.order_id = common.getUrlParams("orderId")
                _self.$http.post(apiUrl + 'workplan_searchModifyLog', params).then((response) => {
                    let retData = response.data.info
                    $modifyLogTable.bootstrapTable('load', {
                        data: retData
                    })
                }, (response) => {
                    // error callback
                    common.dealErrorCommon(_self, response)
                })
            }

            function updateReasonFilter(value) {
                if (_self.pagePara.updateReasonList) {
                    for (let i = 0; i < _self.pagePara.updateReasonList.length; i++) {
                        if (_self.pagePara.updateReasonList[i].id === value) {
                            return common.remarkFormatter(_self.pagePara.updateReasonList[i].text)
                        }
                    }
                }
                return ''
            }

            function initModifyLogTable() {
                $modifyLogTable.bootstrapTable({
                    height: 140,
                    columns: [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('text', '施工节点'),
                        common.BTRowFormatWithFormatterAlignLeft('update_content', '修改内容',common.remarkFormatter),
                        common.BTRowFormatWithFormatterAlignLeft('update_reason', '修改原因', updateReasonFilter),
                        common.BTRowFormatWithFormatterAlignLeft('remark', '备注',common.remarkFormatter),
                        common.BTRowFormat('created_at', '修改日期'),
                        common.BTRowFormat('name', '修改人')
                    ],
                    idField: 'customercontact_id',
                    uniqueId: 'customercontact_id',
                    toolbar: '#toolbar',
                    clickToSelect: false,
                    showRefresh: false,
                    locale: 'zh-CN',
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($modifyLogTable)
            }

            $(function() {
                getGantte();
                initPageWatch();
            })


        }
    },
    methods: {
        initGantt: function(retData) {
            let _self = this;

            let dataArr = [];
            if (retData != null && retData.length > 0) {
                for (var i = 0; i < retData.length; i++) {
                    let obj = new Object();
                    obj.id = retData[i].gantttasks_id;
                    obj.start_date = retData[i].start_date;
                    obj.text = retData[i].text;
                    obj.duration = retData[i].duration;
                    obj.progress = retData[i].progress;
                    obj.progressColor = "#0fd16f";
                    obj.leader_id = retData[i].leader_id;
                    obj.leader_name = retData[i].leader_name;
                    obj.phone = retData[i].phone;
                    obj.open = true;
                    dataArr.push(obj)
                }
            }
            var tasks = {
                data: dataArr,
                links: []
            };

            window.setTimeout(function() {
                gantt.clearAll();

                gantt.parse(tasks);

                $(".gantt_grid_head_add").css("display", "none");

                $(".addLeader").click(function() {
                    let curLeaderInfo = $(this).parent().find("#leaderId").val();
                    $("#leader_info_id").html(curLeaderInfo);
                    curTask = $(this).parent();
                    $('#leaderInfoModal').modal('show')
                });

            }, 1000)

            gantt.attachEvent("onAfterTaskDrag", function(id, mode) {
                var task = gantt.getTask(id);
                curTask = gantt.getTask(id);
                var params = {};
                params.gantttasks_id = task.id;

                if (mode == gantt.config.drag_mode.progress) {
                    //任务进度变化
                    var pr = Math.floor(task.progress * 10) / 10;
                    params.progress = pr;

                    _self.$http.post(apiUrl + 'workplan_modify', params).then((response) => {

                        console.log('modify success')
                    }, (response) => {
                        console.log('modify error')
                        common.dealErrorCommon(_self, response)
                    })
                } else {
                    //任务时间变化
                    params.start_date = task.start_date;
                    params.duration = task.duration;

                    $("#gantt_remark").val("");
                    $("#update_reason").val(null).trigger('change');
                }
            });
        },
        searchAct: function(event) {
            let _self = this;
            var params = new Object();
            params.order_id = _self.order.order_id;
            _self.$http.post(apiUrl + 'workplan_search', params).then((response) => {
                let retData = response.data.info

                _self.initGantt(retData);

                console.log('get gantt success')
            }, (response) => {
                console.log('get gantt error')
                common.dealErrorCommon(_self, response)
            })
        },
        modifyAct: function(event) {
            let _self = this;
            var params = {}
            if (!common.selectCheck(_self, 'leader_id', '请选择负责人')) return;
            params.gantttasks_id = curTaskId;
            params.leader_id = $("#leader_id").val()[0];
            $('#AddModal').modal('hide')
            _self.searchAct();
        },
        generatePlan: function(event) {
            let _self = this;
            if (ifExist == 1) {
                common.dealConfrimCommon("施工计划将重新生成，是否继续？", function() {
                    _self.doGenerate()
                });
            } else {
                _self.doGenerate()
            }
        },
        doGenerate: function(event) {
            let _self = this;
//            var start_date = $("#start_date").val();
//            if (start_date == null || start_date == "") {
//                common.dealPromptCommon('请选择开工日期')
//                return
//            }
            let actual_start_date = $('#actual_start_date').val();
            if (!actual_start_date) {
                return common.dealPromptCommon('请选择实际开工日期')
            }

//            if (!common.selectCheck(_self, 'search_templete_id', '请选择施工节点模板')) return
            //sat_off_id
            var params = {};
//            let sat_off = 1;
//            let sun_off = 1;
//            if ($("#sat_off_id").is(':checked')) {
//                sat_off = 0;
//            }
//            if ($("#sun_off_id").is(':checked')) {
//                sun_off = 0;
//            }
//            params.template_id = $("#search_templete_id").val()[0];
//            params.start_date = start_date;
//            //0：周六工作，1：周六休息
//            params.sat_off = sat_off;
//            //0：周日工作，1：周日休息
//            params.sun_off = sun_off;
            params.order_id = _self.order.order_id;
            params.produce_id = _self.order.produce_id;
            params.actual_start_date = actual_start_date;

            _self.$http.post(apiUrl + 'workplan_generate_plan', params).then((response) => {
                _self.searchAct();
                ifExist = 1;

                let parameter = {}
                parameter.order_id = _self.order.order_id
                console.log('generate success')
            }, (response) => {
                console.log('generate error')
                common.dealErrorCommon(_self, response)
            })

        },
        modifyGanttInfo: function() {
            let _self = this;
            var task = curTask;
            _self.workRow = {}
            if (_self.checkState('WORKING') && !common.selectCheck(_self, 'update_reason', '请选择修改原因')) return;
            _self.workRow.gantt_remark = $("#gantt_remark").val();
            _self.workRow.gantttasks_id = task.id;
            _self.workRow.start_date = task.start_date;
            _self.workRow.duration = task.duration;
            _self.workRow.user_id = common.getStoreData('userinfo').user_id;
            _self.workRow.order_id = _self.order.order_id;

            _self.$http.post(apiUrl + 'workplan_modify_d', _self.workRow).then((response) => {
                $('#ganttLogModal').modal('hide');

                _self.getChangeLog();
                console.log('modify success')
            }, (response) => {
                console.log('modify error')
                common.dealErrorCommon(_self, response)
            })
        },
        getChangeLog: function() {
            let _self = this;

            let params = {};
            params.order_id = common.getUrlParams("orderId")
            _self.$http.post(apiUrl + 'workplan_searchModifyLog', params).then((response) => {
                let retData = response.data.info
                $('#modifyLogTable').bootstrapTable('load', {
                    data: retData
                })
            }, (response) => {
                // error callback
                common.dealErrorCommon(_self, response)
            })
        },
        checkState: function(state, event) {
            let _self = this
            if (_self.order.statedict) {
                if (state in _self.order.statedict) return true
            }
            return false
        },
        showImg: function(url, flag, event) {
            if (flag == 1) {
                //监理大图
                $("#sBigPic").attr('src', url);

            } else if (flag == 2) {
                //工长大图
                $("#fBigPic").attr('src', url);
            }
        },
        saveOrder:function () {
            let _self = this;
            let actual_start_date = $('#actual_start_date').val();
            if (!actual_start_date) {
                return common.dealPromptCommon('请选择实际开工日期')
            }
            _self.order.actual_start_date = actual_start_date;
            _self.$http.post(apiUrl + 'saveOrder', _self.order).then((response) => {
                _self.$parent.refreshOrder();
            }, (response) => {
                common.dealErrorCommon(_self, response)
            })
        }
    }
}
</script>
<style scoped>
.div-style {
    margin-bottom: 10px;
}

.div-style div {
    margin-right: 20px;
}

.closeModal {
    position: absolute;
    right: 10px;
    display: inline-block;
    width: 20px;
    height: 20px;
    text-align: center;
    border-radius: 10px;
    background: #fff;
    color: #937867;
    line-height: 20px;
    font-weight: 900;
    cursor: pointer;
}

.build-heard {
    height: 40px;
    background: #937867;
    color: #fff;
    position: relative;
}

.build-Main {
    padding-left: 20px;
    padding-right: 20px;
    overflow: scroll;
    height: 630px;
    width: 600px;
}

.bulid-thumbnail {
    width: 46px;
    height: 36px;
    margin-bottom: 8px;
    background: #edeeef;
    border: none;
}

.bulid-thumbnail img {
    width: 46px;
    height: 36px;
    cursor: pointer;
}

.bulid-bigPicture {
    display: inline-block;
    position: absolute;
    top: 0px;
    left: 65px;
    width: 470px;
    height: 344px;
    background: #edeeef;
}

.bulid-bigPicture img {
    width: 470px;
    height: 344px;
}

.build-pictureMain {
    height: 375px;
    margin-top: 10px;
    margin-bottom: 10px;
    border-bottom: 1px solid grey;
    position: relative;
}

.build-thumbnailMain {
    width: 60px;
    height: 350px;
    display: inline-block;
    position: absolute;
}

.build-info1 {
    position: relative;
    line-height: 29px
}

.build-info1 img {
    width: 30px;
    height: 30px;
    border-radius: 50%
}

.build-info1 span:nth-child(2) {
    margin-left: 10px
}

.build-info1 span:nth-child(3) {
    position: absolute;
    right: 10px;
}
</style>
