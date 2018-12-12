<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">会议管理</a></li>
            <li class="active">会议详情</li>
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

                            <div class="form-group col-sm-3">
                                <div class="input-group ">
                                    <span class="input-group-addon">会议地点</span>
                                    <div>
                                        <input class="form-control" v-model="meeting.meetingroom_name" readonly>
                                    </div>
                                </div>
                            </div>

                            <!--<div class="form-group col-sm-2">-->
                                <!--<div class="input-group ">-->
                                    <!--<span class="input-group-addon">设备状态</span>-->
                                    <!--<div style="width:100px;">-->
                                        <!--<select class="form-control select2" id="meetingequipment_state" disabled></select>-->
                                    <!--</div>-->
                                <!--</div>-->
                            <!--</div>-->
                            <div class="form-group col-sm-2">
                                <div class="input-group" style="padding-left: 20px;">
                                    <button id="addMMD" v-show="meeting_state !=1 " class="btn btn-info" v-on:click="addMMD">提交</button>
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
                            <!--<li class="active" @click="setData('demandEquipment', $event)">-->
                                <!--<a href="#DemandEquipmentControl" data-toggle="tab">-->
                                    <!--<span class="visible-xs">需要设备</span>-->
                                    <!--<span class="hidden-xs">需要设备</span>-->
                                <!--</a>-->
                            <!--</li>-->
                            <li class="active" @click="setData('participantsInformation', $event)">
                                <a href="#ParticipantsInformationControl" data-toggle="tab">
                                    <span class="visible-xs">参与人员</span>
                                    <span class="hidden-xs">参与人员</span>
                                </a>
                            </li>
                        </ul>

                        <div class="tab-content">
                            <!--<div class="tab-pane fade active in" id="DemandEquipmentControl">-->
                                <!--<demandEquipmentControl :meeting="demandEquipment" :pagePara="pagePara"></demandEquipmentControl>-->
                            <!--</div>-->
                            <div class="tab-pane fade active in" id="ParticipantsInformationControl">
                                <participantsInformationControl :meeting="participantsInformation" :pagePara="pagePara"></participantsInformationControl>
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
const apiUrl = '/api/nca/baseconfig/NCAMeetingManageControl?method=';

// import demandEquipmentControl from './NCADemandEquipmentControl.vue'
import participantsInformationControl from './NCAParticipantsInformationControl.vue'

export default {
    data: function() {
        return {
            pagePara:{},
            meeting: {},
            demandEquipment: {},
            participantsInformation: {},
            tabInfo: '',
            meetingroom_name: '',
            meeting_id: '',
            meeting_state: ''
        }
    },
    components: {
        // demandEquipmentControl,
        participantsInformationControl
    },
    name: 'NCAMeetingManageDetailControl',
    mounted: async function() {
        this.getMeetingManageInformation();
        this.getMeetingManageDetailInformation();

    },
    watch: {
        meeting: function() {
            if (this.meeting && !this.tabInfo) {
                this.setData('participantsInformation');
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
                this.meeting_state = this.meeting.meeting_state
                $('#meetingequipment_state').val(this.meeting.meetingequipment_state).trigger('change');
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        getMeetingManageInformation: async function () {
            try {
                let response = await this.$http.post(apiUrl + 'init');
                this.pagePara = JSON.parse(JSON.stringify(response.data.info));
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

            this.$http.post(apiUrl + 'submit', params).then((response)=>{
                let retData = response.data.info;
                this.meeting_state = retData.meeting_state
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
