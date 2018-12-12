<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">会议记录管理</a></li>
            <li class="active">会议记录管理详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12 col-sm-12">
                <div class="widget widget-stats bg-black-lighter">
                    <div class="stats-info">
                        <div class="row">
                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">会议议题</span>
                                    <div>
                                        <input class="form-control" v-model="meeting.meeting_name" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">会议时间</span>
                                    <div>
                                        <input class="form-control" v-model="meeting.start_time" readonly>
                                    </div>
                                </div>
                            </div>

                            <div class="form-group col-sm-2">
                                <div class="input-group ">
                                    <span class="input-group-addon">会议地点</span>
                                    <div>
                                        <input class="form-control" v-model="meeting.meetingroom_name" readonly>
                                    </div>
                                </div>
                            </div>
                            <div class="form-group col-sm-2">
                                <div class="input-group" style="padding-left: 20px;">
                                    <button id="addMMD" class="btn btn-info" v-show="meeting_remark_state !=1 " v-on:click="addMMD">提交</button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-md-12">
                <div class="row">
                    <div class="col-md-12">
                        <ul class="nav nav-tabs">
                            <!--<li class="active" @click="setData('meetingDemandEquipment', $event)">-->
                                <!--<a href="#MeetingDemandEquipmentControl" data-toggle="tab">-->
                                    <!--<span class="visible-xs">需要设备</span>-->
                                    <!--<span class="hidden-xs">需要设备</span>-->
                                <!--</a>-->
                            <!--</li>-->
                            <li class="active" @click="setData('meetingParticipantsInformation', $event)">
                                <a href="#MeetingParticipantsInformationControl" data-toggle="tab">
                                    <span class="visible-xs">参与人员</span>
                                    <span class="hidden-xs">参与人员</span>
                                </a>
                            </li>
                            <li class="" @click="setData('minutesInformation', $event)">
                                <a href="#MinutesInformationControl" data-toggle="tab">
                                    <span class="visible-xs">会议记录</span>
                                    <span class="hidden-xs">会议记录</span>
                                </a>
                            </li>
                            <li class="" @click="setData('decisionInformation', $event)">
                                <a href="#DecisionInformationControl" data-toggle="tab">
                                    <span class="visible-xs">会议决议</span>
                                    <span class="hidden-xs">会议决议</span>
                                </a>
                            </li>
                            <li class="" @click="setData('followUpMattersInformation', $event)">
                                <a href="#FollowUpMattersInformationControl" data-toggle="tab">
                                    <span class="visible-xs">跟进事项</span>
                                    <span class="hidden-xs">跟进事项</span>
                                </a>
                            </li>
                        </ul>

                        <div class="tab-content">
                            <!--<div class="tab-pane fade active in" id="MeetingDemandEquipmentControl">-->
                                <!--<meetingDemandEquipmentControl :meeting="meetingDemandEquipment" :pagePara="pagePara"></meetingDemandEquipmentControl>-->
                            <!--</div>-->
                            <div class="tab-pane fade active in" id="MeetingParticipantsInformationControl">
                                <meetingParticipantsInformationControl :meeting="meetingParticipantsInformation" :pagePara="pagePara"></meetingParticipantsInformationControl>
                            </div>
                            <div class="tab-pane fade" id="MinutesInformationControl">
                                <minutesInformationControl :meeting="minutesInformation" :pagePara="pagePara"></minutesInformationControl>
                            </div>
                            <div class="tab-pane fade" id="DecisionInformationControl">
                                <decisionInformationControl :meeting="decisionInformation" :pagePara="pagePara"></decisionInformationControl>
                            </div>
                            <div class="tab-pane fade" id="FollowUpMattersInformationControl">
                                <followUpMattersInformationControl :meeting="followUpMattersInformation" :pagePara="pagePara"></followUpMattersInformationControl>
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
const apiUrl = '/api/nca/baseconfig/NCAMeetingMinuteControl?method=';

// import meetingDemandEquipmentControl from './NCAMeetingDemandEquipmentControl.vue'
import meetingParticipantsInformationControl from './NCAMeetingParticipantsInformationControl.vue'
import minutesInformationControl from './NCAMinutesInformationControl.vue'
import decisionInformationControl from './NCADecisionInformationControl.vue'
import followUpMattersInformationControl from './NCAFollowUpMattersInformationControl.vue'

export default {
    data: function() {
        return {
            pagePara:{},
            meeting: {},
            meetingDemandEquipment: {},
            meetingParticipantsInformation: {},
            minutesInformation: {},
            decisionInformation: {},
            followUpMattersInformation: {},
            tabInfo: '',
            meetingroom_name: '',
            meeting_id: '',
            meeting_remark_state: ''
        }
    },
    components: {
        // meetingDemandEquipmentControl,
        meetingParticipantsInformationControl,
        minutesInformationControl,
        decisionInformationControl,
        followUpMattersInformationControl
    },
    name: 'NCAMeetingMinuteDetailControl',
    mounted: async function() {
        this.getMeetingManageInformation();
        this.getMeetingManageDetailInformation();

    },
    watch: {
        meeting: function() {
            if (this.meeting && !this.tabInfo) {
                this.setData('meetingParticipantsInformation');
            }
        }
    },
    methods: {
        getMeetingManageDetailInformation: async function () {
            let meeting_id = this.$route.query.meeting_id;
            let param = {
                meeting_id: meeting_id
            };

            try {
                let response = await this.$http.post(apiUrl + 'search_d', param);
                this.meeting = response.data.info.rows[0];
                this.meeting_remark_state = this.meeting.meeting_remark_state
                console.log('this.meeting',this.meeting)
                console.log('this.meeting.meetingequipment_state',this.meeting.meetingequipment_state)
                $('#meetingequipment_state').val(this.meeting.meetingequipment_state).trigger('change');
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        getMeetingManageInformation: async function () {
            try {
                let response = await this.$http.post(apiUrl + 'init');
                this.pagePara = JSON.parse(JSON.stringify(response.data.info));//response.data.info;
                console.log('this.pagePara',this.pagePara)
                common.initSelect2($('#meetingattendee_state'), this.pagePara.meetingroomInfo);
                common.initSelect2($('#meetingequipment_state'), this.pagePara.meetingroomInfo);

            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        setData: async function(tabData, event) {
            await this.getMeetingManageDetailInformation();
            this.tabInfo = tabData;
            this[tabData] = this.meeting;
        },
        refreshEmployee: function(tabData, event) {
            this.getMeetingManageDetailInformation()
        },
        addMMD: function(event) {
            let params ={
                meeting_id: this.$route.query.meeting_id,
                meetingroom_name: this.$route.query.meetingroom_name
            }

            this.$http.post(apiUrl + 'submit_m', params).then((response)=>{
                let retData = response.data.info;
                this.meeting_remark_state = retData.meeting_remark_state
                common.dealSuccessCommon('已提交');
            }, (error)=>{
                common.dealErrorCommon(this, error)
            });
        }
    }
}
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
