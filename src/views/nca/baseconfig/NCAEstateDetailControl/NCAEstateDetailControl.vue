<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb" v-show="!pagePara.thirdUser">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()" >户型及房号管理</a></li>
            <li class="active">户型及房号详情</li>
    </ol>
    <ol v-show="pagePara.thirdUser" class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:history.back()" >地产商楼盘管理</a></li>
        <li class="active">户型及房号详情</li>
    </ol>

    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12 col-sm-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <h4>楼盘信息</h4>
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">省</span>
                                <div>
                                    <input class="form-control" id="province" v-model="estate.province" readonly>
                                </div>
                            </div>
                        </div>
                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">市</span>
                                <div>
                                    <input class="form-control" id="city" v-model="estate.city" readonly>
                                </div>
                            </div>
                        </div>

                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">区</span>
                                <div>
                                    <input class="form-control" id="district" v-model="estate.district" readonly>
                                </div>
                            </div>
                        </div>

                        <div class="form-group col-sm-3">
                            <div class="input-group ">
                                <span class="input-group-addon">地产商</span>
                                <div>
                                    <input class="form-control" id="land_agent" v-model="estate.landagent_name" readonly>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="row">
                        <div class="form-group col-sm-9">
                            <div class="input-group ">
                                <span class="input-group-addon">门牌地址</span>
                                <div>
                                    <input class="form-control" id="address" v-model="estate.address" readonly>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <div class="row">
        <div class="col-md-12">
            <ul class="nav nav-tabs">
                <li class="active" @click="setData('edRoomType', $event)" v-show="!pagePara.thirdUser">
                    <a href="#EDRoomTypeHQControl" data-toggle="tab">
                        <span class="visible-xs">户型管理</span>
                        <span class="hidden-xs">户型管理</span>
                    </a>
                </li>
                <li class="" @click="setData('edRoomNum', $event)">
                    <a href="#EDRoomNumHQControl" data-toggle="tab">
                        <span class="visible-xs">房屋管理</span>
                        <span class="hidden-xs">房屋管理</span>
                    </a>
                </li>
                <li class="" @click="setData('edRoomTeam', $event)" v-show="!pagePara.thirdUser">
                    <a href="#EDRoomTeamHQControl" data-toggle="tab">
                        <span class="visible-xs">团队管理</span>
                        <span class="hidden-xs">团队管理</span>
                    </a>
                </li>
            </ul>

            <div class="tab-content">
                <div class="tab-pane fade active in" id="EDRoomTypeHQControl">
                    <edRoomTypeControl :estate="edRoomType" :pagePara="pagePara"></edRoomTypeControl>
                </div>
                <div class="tab-pane fade" id="EDRoomNumHQControl">
                    <edRoomNumControl :estate="edRoomNum" :pagePara="pagePara"></edRoomNumControl>
                </div>
                <div class="tab-pane fade" id="EDRoomTeamHQControl">
                    <edRoomTeamControl :estate="edRoomTeam" :pagePara="pagePara"></edRoomTeamControl>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEstateControl?method=';

import edRoomTypeControl from './NCAEDRoomTypeControl.vue'
import edRoomNumControl from './NCAEDRoomNumControl.vue'
import edRoomTeamControl from './NCAEDRoomTeamControl.vue'

export default {
    data: function() {
        return {
            estate: {},
            pagePara: {},
            edRoomType: {},
            edRoomNum: {},
            edRoomTeam: {},
            tabInfo: ''
        }
    },
    components: {
        edRoomTypeControl,
        edRoomNumControl,
        edRoomTeamControl
    },
    name: 'NCAEstateDetailControl',
    mounted: async function() {
        this.getRoomType();
        this.getEstate();

    },
    watch: {
        estate: function() {
            if (this.estate && !this.tabInfo) {
                this.setData('edRoomType');
            }
        }
    },
    methods: {
        getEstate: async function () {
            let estateId = this.$route.query.estateId;
            let param = {
                estate_id: estateId
            };

            try {
                let response = await this.$http.post(apiUrl + 'searchEstate', param);
                this.estate = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        getRoomType: async function () {
            try {
                let response = await this.$http.post(apiUrl + 'init');
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        setData: async function(tabData, event) {
            await this.getEstate();
            this.tabInfo = tabData;
            this[tabData] = this.estate;
        },
        refreshEstate: function(tabData, event) {
            this.getEstate()
        }

    }
}
</script>
<style scoped>
</style>
