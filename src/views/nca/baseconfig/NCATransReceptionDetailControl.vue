<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">出差交通接待申请</a></li>
            <li class="active">出差交通接待申请单</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row hide-order-detail-desk">
            <div class="col-md-12">
                <div class="panel panel-inverse" v-show="applyData.trapply_state!=='0' && applyData.trapply_state!=='1'">
                    <div class="panel-heading">
                        <span class="panel-title">审核进度</span>
                        <span style="padding-left: 100px;">申请单编号：{{applyData.trapply_code}}</span>
                        <span style="padding-left: 100px;">申请人：{{applyData.trapply_creator_name}}</span>
                        <span style="padding-left: 100px;">申请单状态：{{applyData.trapply_state_text }}</span>
                        <a style="margin-left: 230px;" class="btn btn-info btn-xs" v-show="show_return == 1" href="javascript:history.back(-1)">返回</a>
                    </div>
                    <div class="panel-body">
                        <div>
                            <ul class="timeline">
                                <!--<li class="li complete">
                                    <div class="timestamp">
                                        <span class="">{{applyData.trapply_creator_name}} 新建申请</span>
                                    </div>
                                    <div class="status">
                                        <h4>{{applyData.created_at}}</h4>
                                    </div>
                                </li>-->
                                <li class="li complete"  v-show="applyData.trapply_state==='3'">
                                    <div class="timestamp">
                                        <span class="">{{applyData.trapply_confirm_name}} 驳回</span>
                                    </div>
                                    <div class="status">
                                        <h4>{{applyData.trapply_confirm_time}}</h4>
                                    </div>
                                </li>
                                <li class="li complete"  v-show="applyData.trapply_state==='2'">
                                    <div class="timestamp">
                                        <span class="">{{applyData.trapply_confirm_name}} 通过</span>
                                    </div>
                                    <div class="status">
                                        <h4>{{applyData.trapply_confirm_time}}</h4>
                                    </div>
                                </li>
                            </ul>
                        </div>
                        <!--<timeLine :order="order"></timeLine>-->
                    </div>
                </div>

                <form  @submit.prevent="saveApply" id="formApply">
                    <!--交通申请-->
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <div class="row pull-right">
                                    <button v-show="inputDisabled==false" type="submit" class="btn btn-info btn-xs" style="margin-right: 20px">保存</button>
                                    <button v-show="inputDisabled==false" type="button" v-on:click="setTask" class="btn btn-info btn-xs" style="margin-right: 20px">审核</button>
                                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning"
                                       data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                                </div>
                            </div>
                            <h4 class="panel-title">出差申请单</h4>
                        </div>
                        <div class="panel-body">
                            <!--出差出发地省，市，区，地址-->
                            <div class="row">
                                <div data-toggle="distpicker" id="distpicker">
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">出差起始地省</span>
                                            <div>
                                                <select class="form-control picker-width" id="province"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">市县</span>
                                            <div>
                                                <select class="form-control picker-width" id="city"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">区镇</span>
                                            <div>
                                                <select class="form-control picker-width" id="district"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">详细地址</span>
                                        <div>
                                            <input class="form-control" id="order_address" v-model="applyData.trapply_trip_origin_detail"
                                                   oninput="if(value.length>1000)value=value.slice(0,1000)" :disabled="inputDisabled">
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!--出差目的地省，市，区，地址-->
                            <div class="row">
                                <div data-toggle="distpicker" id="distpicker2">
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">出差目的地省</span>
                                            <div>
                                                <select class="form-control picker-width" id="province2"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">市县</span>
                                            <div>
                                                <select class="form-control picker-width" id="city2"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                    <div class="form-group col-sm-3">
                                        <div class="input-group">
                                            <span class="input-group-addon">区镇</span>
                                            <div>
                                                <select class="form-control picker-width" id="district2"
                                                        :disabled="inputDisabled"></select>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">详细地址</span>
                                        <div>
                                            <input class="form-control" id="order_address2" v-model="applyData.trapply_trip_termini_detail"
                                                   oninput="if(value.length>1000)value=value.slice(0,1000)" :disabled="inputDisabled">
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <!--出差事由，出差事由分类，非自驾车预计交通工具费用预算(不需填写)，非自驾车预计的交通工具费用预算(不需填写)-->
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">出差事由</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.trapply_trip_reason"
                                                   oninput="if(value.length>100)value=value.slice(0,100)"  :disabled=inputDisabled>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <div class="input-group">
                                        <span class="input-group-addon">出差事由分类</span>
                                        <div>
                                            <select class="form-control select2" id="trapply_trip_reason_type"
                                                    :disabled="inputDisabled"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3" >
                                    <div class="input-group ">
                                        <span class="input-group-addon">自驾车预计的油补费用</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.reimburserank_gas_price" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!--预计住宿费用，预计市内交通补助，预计伙食补助(4个不需填写)-->
                            <div class="row"  >
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">预计住宿费用</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.reimburserank_trip_putup_price" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">预计市内交通补助</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.reimburserank_downtown_traffic_price" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">预计伙食补助</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.reimburserank_meal_price" disabled="disabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">公共交通工具</span>
                                        <div>
                                            <select class="form-control select2"  style="width:100%" id="trapply_traffic_tools" :disabled="inputDisabled || toolsDisable"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3" >
                                    <div class="input-group ">
                                        <span class="input-group-addon">非自驾车预计交通工具费用</span>
                                        <div class="">
                                            <div>
                                                <input class="form-control" v-model="applyData.trapply_traffic_fee" type="number" :disabled="inputDisabled  || applyData.trapply_start_time===null  || gasDisable==false">
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!--派车申请-->
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <div class="row pull-right">
                                    <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning"
                                       data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                                </div>
                            </div>
                            <h4 class="panel-title">派车申请单</h4>
                        </div>
                        <div class="panel-body">
                            <div class="row">
                                <!--交通方式，车辆申请，预计公里数，派车费用报销模式-->
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">交通方式</span>
                                        <div>
                                            <select class="form-control select2" id="trapply_trans_way"
                                                    :disabled="inputDisabled" @change="changeVehicleDisable"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">车辆申请</span>
                                        <div>
                                            <select class="form-control select2" :disabled="inputDisabled || vehicleDisable"
                                                    id="trapply_vehicle_apply"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">预计公里数</span>
                                        <div>
                                            <input class="form-control" type="number"  v-model="applyData.trapply_vehicle_distance" onkeyup="this.value=this.value.replace(/\D/g,'')"
                                                   oninput="if(value.length>1000)value=value.slice(0,1000)" :disabled="inputDisabled || gasDisable" min="0">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">派车费用报销模式</span>
                                        <div>
                                            <select class="form-control select2"  style="width:100%"
                                                    id="trapply_vehicle_review_type" :disabled="inputDisabled || vehicleDisable" ></select>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!--接待申请-->
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-warning"
                                   data-click="panel-collapse"><i class="fa fa-minus"></i></a>
                            </div>
                            <h4 class="panel-title">接待申请单</h4>
                        </div>
                        <div class="panel-body">
                            <!--接待事由，接待事由分类，接待对象总人数，接待主要对象姓名-->
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待事由</span>
                                        <div>
                                            <input class="form-control" :disabled="inputDisabled" oninput="if(value.length>100)value=value.slice(0,100)"
                                                   v-model="applyData.trapply_reception_reason">
                                        </div>
                                    </div>
                                </div>

                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">住宿登记房间个数</span>
                                        <div>
                                            <input class="form-control" type="number" :disabled="inputDisabled"
                                                   oninput="if(value.length>10)value=value.slice(0,10)" min="0"
                                                   onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="applyData.trapply_reception_room_num">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待对象总人数</span>
                                        <div>
                                            <input class="form-control" type="number" :disabled="inputDisabled" min="0"
                                                   onkeyup="this.value=this.value.replace(/\D/g,'')" v-model="applyData.trapply_reception_crew_num" >
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待主要对象姓名</span>
                                        <div>
                                            <input class="form-control" :disabled="inputDisabled" oninput="if(value.length>100)value=value.slice(0,100)"
                                                   v-model="applyData.trapply_reception_object">
                                        </div>
                                    </div>
                                </div>
                            </div>

                            <!--接待费用报销模式,接待主要对象接待级职，接待主要对象的随行人，陪同人员-->
                            <div class="row">
                                <div class="form-group col-sm-6">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待事由分类</span>
                                        <div>
                                            <select class="form-control select2" id="trapply_reception_reason_type"
                                                    :disabled="inputDisabled"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-6">
                                    <div class="input-group ">
                                        <span class="input-group-addon">陪同人员</span>
                                        <div class="col-sm-11">
                                            <input  class="form-control select2" id="trapply_recetion_crew_ids" v-on:click="choiceGroup()" disabled
                                                     style="width:100%"></input>
                                        </div>
                                        <div class="col-sm-1 padding-top-7">
                                            <label class="btn btn-info btn-xs fileupload-button" v-on:click="choiceGroup(2)" :disabled="inputDisabled">选择</label>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待费用报销模式</span>
                                        <div>
                                            <select  class="form-control select2" id="trapply_reception_review_type"
                                                     style="width:100%" :disabled="inputDisabled"></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group">
                                        <span class="input-group-addon">接待主要对象接待级职</span>
                                        <div>
                                            <select  class="form-control select2" id="trapply_reception_review_level"
                                                     style="width:100%" :disabled="inputDisabled"  ></select>
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">赠送礼品及额外活动费用</span>
                                        <div>
                                            <input class="form-control" type="number" step="0.01" :disabled="inputDisabled" min="1"
                                                   oninput="if(value.length>1000)value=value.slice(0,1000)"
                                                   onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                                   v-model="applyData.trapply_reception_extra_fee">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-3" >
                                    <div class="input-group ">
                                        <span class="input-group-addon">预计接待费用</span>
                                        <div class="">
                                            <div>
                                                <input class="form-control" v-model="applyData.reimburserank_reception_price"  disabled="disabled">
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!--预计礼品活动费用，   预计接待住宿费用 预计接待费用(2个不需填写)-->
                            <div class="row">
                                <div class="form-group col-sm-3" >
                                    <div class="input-group ">
                                        <span class="input-group-addon">预计接待住宿费用</span>
                                        <div>
                                            <input class="form-control" v-model="applyData.reimburserank_reception_putup_price" disabled="disabled" >
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="panel panel-inverse" style="background: #d9e0e7">
                        <div class="panel-body">
                            <div class="row">
                                <div class="form-group col-sm-3">
                                    <div class="input-group ">
                                        <span class="input-group-addon">申请预借费用(本位币)</span>
                                        <div>
                                            <input class="form-control" type="number" v-model="applyData.trapply_pre_fee" step="0.01" min="1"
                                                   oninput="if(value.length>1000)value=value.slice(0,1000)"
                                                   onkeyup="value=value.replace(/[^\d\.]/g,'');value = value.replace(/\.{2,}/g,'.');value = value.replace('.','$#$').replace(/\./g,'').replace('$#$','.');value = value.replace(/^\./g,'');value = value.replace(/^(\-)*(\d+)\.(\d\d).*$/,'$1$2.$3');"
                                                   data-parsley-required="true" :disabled="inputDisabled">
                                        </div>
                                    </div>
                                </div>
                                <div class="form-group col-sm-7">
                                    <div class="input-group ">
                                        <span class="input-group-addon">上述业务发生时间</span>
                                        <div class="form-inline">
                                            <div class="form-group">
                                                <input type="text" class="form-control" id="start_date"
                                                       placeholder="开始时间"  data-parsley-required="true" autocomplete="off" :disabled="inputDisabled" />
                                            </div>
                                            <div class="form-group">
                                                <span>-</span>
                                            </div>
                                            <div class="form-group">
                                                <input type="text" class="form-control" id="end_date"
                                                       placeholder="结束时间"  data-parsley-required="true" autocomplete="off" :disabled="inputDisabled" />
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!--<div class="row pull-right">-->
                                <!--<button v-show="inputDisabled==false" type="submit" class="btn btn-info" style="margin-right: 20px">保存</button>-->
                                <!--<button v-show="inputDisabled==false" type="button" v-on:click="setTask" class="btn btn-info" style="margin-right: 20px">审核</button>-->
                            <!--</div>-->
                        </div>
                    </div>
                </form>

            </div>
        </div>

        <userSelectDialog :usergroupId="usergroupId" :users="users"></userSelectDialog>
       <!-- <userSelectDialog :usergroupId="usergroupId" :users="users" :mulityFlag="'1'"></userSelectDialog>-->
    </div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCATransReceptionDetailControl?method='
import userSelectDialog from '../../../components/common/userSelectDialog.vue'

export default {
    data: function() {
        return {
            initData:{},
            inputDisabled:false,
            vehicleDisable:true,
            gasDisable:true,
            toolsDisable:true,
            isUseCar:false,
            taskPerformer:'',
            hideFee:false,
            getData:{},
            usergroupId: '',
            users: [],
            eventHub: new Vue(),
            trapplyId:common.getUrlParams("trapplyId"),
            trapplyCode:common.getUrlParams("trapplyCode"),
            userinfo: common.getStoreData('userinfo'),
            applyData: {
                //交通申请详情
                trapply_trip_reason:'',//出差事由
                trapply_state:'',//审核状态
                trapply_state_text:'',
                trapply_trip_reason_type:'',//出差事由分类
                trapply_trip_origin_prov:'',//出差起始地省
                trapply_trip_origin_city:'',//出差起始地市
                trapply_trip_origin_dist:'',//出差起始地区
                trapply_trip_origin_detail:'',//出差起始地详细地址
                trapply_trip_termini_prov:'',//出差目的地省
                trapply_trip_termini_city:'',//出差目的地市
                trapply_trip_termini_dist:'',//出差目的地区
                trapply_trip_termini_detail:'',//出差目的地详细地址
                trapply_trans_way:'',//交通方式
                trapply_vehicle_apply:'',//车辆申请
                trapply_vehicle_review_type:'',//派车费用报销模式
                trapply_vehicle_distance:'',//预计公里数
                trapply_vehicle_remark:'',//公里数说明
                trapply_traffic_tools:'',//交通工具

                //接待申请详情
                trapply_reception_reason:'',//接待事由
                trapply_reception_reason_type:'',//接待事由分类
                trapply_reception_object:'',//接待主要对象姓名
                trapply_reception_room_num:'',//住宿登记房间个数
                trapply_reception_review_type:'',//接待费用报销模式
                trapply_reception_review_level:'',//接待主要对象接待级职
                trapply_reception_crew_num:'',//接待对象随行人数
                trapply_reception_extra:'',//赠送礼品或额外活动
                trapply_reception_extra_fee:'',//额外活动费用预算
                trapply_recetion_crew_ids:'',//陪同人员IDs
                trapply_recetion_crew_names:'',//陪同人员名单

                trapply_start_time:'',//申请时间起始
                trapply_end_time:'',//申请时间截止
                trapply_pre_fee:'', //申请预借费用

                reimburserank_reception_putup_price:"",//接待住宿标准
                reimburserank_trip_putup_price:'',//出差住宿标准
                reimburserank_downtown_traffic_price:"",//市内交通补助标准
                reimburserank_meal_price:"",//伙食补助标准
                reimburserank_reception_price:"",//接待费标准
                reimburserank_gas_price:"",//油补标准
                reimburserank_traffic_price:"",//预计用交通金额
                trapply_traffic_fee:"",//交通金额
            },
            show_return: 0,
            toolsArray: {},
        }
    },
    name: 'transReceptionListControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self=this;
        _self.transReceptionTable = $('#transReceptionTable');
        _self.startDate = $('#start_date');
        _self.endDate = $('#end_date');
        _self.getInitData();

        common.initStartDatepicker(_self.startDate,_self.endDate);
        common.initEndDatepicker(_self.endDate,_self.startDate);

        $("#trapply_trans_way").off().on("change",function(){
            _self.changeVehicleDisable();
        });
        $("#trapply_reception_review_level").off().on("change",function(){
            _self.setTrapplyRCrewNum();

        });

    },
    methods: {
        //获得静态下拉列表
        getInitData:async function(){
            let _self=this;
            try{
                let response = await _self.$http.post(apiUrl + 'init',{});
                _self.initSelect2(response.data.info);
                _self.getApplyData(response.data.info);
                _self.show_return = _self.$route.query.show_return;
                console.log('show_return',_self.show_return)
            }catch (error){
                common.dealErrorCommon(this, error)
            }
        },

        //获得交通接待申请单详情
        getApplyData: async function(data) {
            let _self = this;
            try {
                var params={};
                if(_self.trapplyId){
                    params.trapply_id=_self.trapplyId;
                }
                if(_self.trapplyCode){
                    params.trapply_code=_self.trapplyCode
                }
                let response = await _self.$http.post(apiUrl + 'search', params);
                if( response.body.info.total>0){
                    _self.applyData=response.body.info.rows[0];
                    _self.getData=response.body.info.rows[0];
                    _self.formateUserIds(_self.applyData.trapply_recetion_crew_ids);
                    $('#trapply_trip_reason_type').val(_self.applyData.trapply_trip_reason_type).trigger('change');
                    $('#trapply_trans_way').val(_self.applyData.trapply_trans_way).trigger('change');
                    $('#trapply_vehicle_review_type').val(_self.applyData.trapply_vehicle_review_type).trigger('change');
                    $('#trapply_reception_reason_type').val(_self.applyData.trapply_reception_reason_type).trigger('change');
                    $('#trapply_reception_review_type').val(_self.applyData.trapply_reception_review_type).trigger('change');
                    $('#trapply_reception_review_level').val(_self.applyData.trapply_reception_review_level).trigger('change');
                    $('#trapply_vehicle_apply').val(_self.applyData.trapply_vehicle_apply).trigger('change');
                   /* $('#trapply_recetion_crew_ids').val(_self.formateUserIds(2,_self.applyData.trapply_recetion_crew_ids)).trigger('change');*/
                    $('#trapply_traffic_tools').val(_self.applyData.trapply_traffic_tools).trigger('change');

                    $('#province').val(_self.applyData.trapply_trip_origin_prov).trigger("change");
                    $('#city').val(_self.applyData.trapply_trip_origin_city).trigger("change");
                    $('#district').val(_self.applyData.trapply_trip_origin_dist).trigger("change");
                    $('#province2').val(_self.applyData.trapply_trip_termini_prov).trigger("change");
                    $('#city2').val(_self.applyData.trapply_trip_termini_city).trigger("change");
                    $('#district2').val(_self.applyData.trapply_trip_termini_dist).trigger("change");
                    $('#start_date').val(_self.applyData.trapply_start_time);
                    $('#end_date').val(_self.applyData.trapply_end_time);

                    _self.formatStatus(data);

                    }
            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        },
        //下拉框 初始化
        initSelect2:function(data){
            let _self = this;
            _self.initData=data;
            common.initSelect2Wrap($('#trapply_trip_reason_type'), data.trapply_trip_reason_type_arr);
            common.initSelect2($('#trapply_trans_way'), data.trapply_trans_way_arr);
            common.initSelect2($('#trapply_vehicle_review_type'), data.trapply_vehicle_review_type_arr);
            common.initSelect2Wrap($('#trapply_reception_reason_type'), data.trapply_reception_reason_type_arr);
            common.initSelect2($('#trapply_reception_review_type'), data.trapply_reception_review_type_arr);

            common.initSelect2($('#trapply_reception_review_level'), data.reimburSerankArr);
            common.initSelect2($('#trapply_vehicle_apply'), data.vehicleArr);
            /*$('#trapply_recetion_crew_ids').select2({
                language: 'zh-CN',
                tags: false,
                width: '100%',
                data: data.userArr,//userArr
                multiple: true
            });*/

            $('#distpicker').distpicker({
                placeholder: true
            });
            $('#distpicker2').distpicker({
                placeholder: true
            });
            _self.$http.post(apiUrl + 'traffictools', {user_id: common.getStoreData('userinfo').user_id}).then((response) => {
                _self.toolsArray = response.data.info.tools;
                common.initSelect2($('#trapply_traffic_tools'), _self.toolsArray);
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },

        //提交审核
        saveApply: async function(){
            let _self=this;

            _self.applyData.trapply_trip_reason_type= common.getSelect2Val('trapply_trip_reason_type');
            _self.applyData.trapply_trans_way= common.getSelect2Val('trapply_trans_way');
            _self.applyData.trapply_vehicle_review_type= common.getSelect2Val('trapply_vehicle_review_type');
            _self.applyData.trapply_reception_reason_type= common.getSelect2Val('trapply_reception_reason_type');
            _self.applyData.trapply_reception_review_type= common.getSelect2Val('trapply_reception_review_type');

            _self.applyData.trapply_reception_review_level= common.getSelect2Val('trapply_reception_review_level');
            _self.applyData.trapply_vehicle_apply= common.getSelect2Val('trapply_vehicle_apply');
            _self.applyData.trapply_traffic_tools= common.getSelect2Val('trapply_traffic_tools');
            /*_self.applyData.trapply_recetion_crew_ids= _self.formateUserIds(1);*/

            _self.applyData.trapply_trip_origin_prov=$('#province').val();
            _self.applyData.trapply_trip_origin_city=$('#city').val();
            _self.applyData.trapply_trip_origin_dist=$('#district').val();
            _self.applyData.trapply_trip_termini_prov=$('#province2').val();
            _self.applyData.trapply_trip_termini_city=$('#city2').val();
            _self.applyData.trapply_trip_termini_dist=$('#district2').val();

            _self.applyData.trapply_start_time=$('#start_date').val();
            _self.applyData.trapply_end_time=$('#end_date').val();

            if(!_self.applyData.trapply_id){
                return common.dealWarningCommon("没有订单信息！");
            }
            if(_self.applyData.customer_reimburserank_id===null){
                return common.dealWarningCommon("本账户没有接待级职，请联系相关人员进行设置！");
            }

            if(_self.applyData.trapply_reception_review_level!='' && _self.applyData.trapply_reception_crew_num==''){
                return common.dealWarningCommon("请填写接待对象总人数！");

            }
            if(_self.applyData.trapply_start_time=='' || _self.applyData.trapply_end_time=='' || _self.applyData.trapply_pre_fee==''){
                return common.dealWarningCommon("申请预借费用和申请时间为必填项，请检查输入信息");
            }
            if(_self.applyData.trapply_traffic_fee>_self.applyData.reimburserank_traffic_price){
                return common.dealWarningCommon("预计非自驾车预计交通工具费为"+_self.applyData.reimburserank_traffic_price+"，非自驾车预计交通工具费用不能大于预计费用！");

            }

            let params=_self.applyData;

            _self.$http.post(apiUrl + 'modify', params).then((response) => {
                if(response.body.errno==0){
                    common.dealSuccessCommon('保存成功');
                    _self.getInitData();
                }
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },

        //申请审核
        setTask:async function () {
            let _self=this;
            if(_self.getData.trapply_start_time==='' || _self.getData.trapply_start_time===null){
                return common.dealWarningCommon("申请预借费用和申请时间为必填项，请先填写并保存详情信息！");
            }
            if(_self.getData.trapply_end_time==='' || _self.getData.trapply_end_time===null){
                return common.dealWarningCommon("申请预借费用和申请时间为必填项，请先填写并保存详情信息！");
            }
            if(_self.getData.trapply_pre_fee==='' || _self.getData.trapply_pre_fee===null){
                return common.dealWarningCommon("申请预借费用和申请时间为必填项，请先填写并保存详情信息！");
            }
            let params=_self.applyData;
            _self.$http.post(apiUrl + 'setTask', params).then((response) => {
                _self.getApplyData(_self.initData);
                common.dealSuccessCommon('提交成功');
            }, (response) => {
                common.dealErrorCommon(_self, response)
            });
        },

        //获得状态text
        formatStatus:function (data) {
            let _self=this;
            let statusArr=data.trapply_state_arr;
            for(let i of statusArr){
                if(_self.applyData.trapply_state===i.id){
                    _self.applyData.trapply_state_text=i.text;
                }
            }
            if(_self.applyData.trapply_state==='1' || _self.applyData.trapply_state==='2' || _self.applyData.trapply_creator_id!==_self.userinfo.user_id){
                _self.inputDisabled=true;
            }
            if (_self.applyData.trapply_creator_id===_self.userinfo.user_id && _self.applyData.customer_reimburserank_id === null) {
                common.dealWarningCommon("本账户没有接待级职，请联系相关人员进行设置,否则无法提交申请！");
            }
        },

        //更改车辆选择disable
        changeVehicleDisable:function () {
            let _self=this;
            let trapply_trans_way= common.getSelect2Val('trapply_trans_way');
            if(trapply_trans_way==='3'){
                $('#trapply_traffic_tools').val('').trigger('change');
                _self.vehicleDisable=false;
                _self.gasDisable=false;
                _self.toolsDisable=true;
                if(_self.applyData.trapply_vehicle_distance === '' || _self.applyData.trapply_vehicle_distance === undefined){
                    _self.applyData.trapply_vehicle_distance=0;
                }
            }else if(trapply_trans_way==='2'){
                $('#trapply_vehicle_apply').val('').trigger('change');
                $('#trapply_traffic_tools').val('').trigger('change');
                _self.vehicleDisable=true;
                _self.gasDisable=false;
                _self.toolsDisable=true;
                if(_self.applyData.trapply_vehicle_distance === '' || _self.applyData.trapply_vehicle_distance === undefined){
                    _self.applyData.trapply_vehicle_distance=0;
                }
            }else if(trapply_trans_way===''){
                $('#trapply_traffic_tools').val('').trigger('change');
                _self.gasDisable=false;
                _self.toolsDisable=true;
                if(_self.applyData.trapply_traffic_fee === '' || _self.applyData.trapply_traffic_fee === undefined){
                    _self.applyData.trapply_traffic_fee=0;
                }
                if(_self.applyData.reimburserank_gas_price === '' || _self.applyData.reimburserank_gas_price === undefined){
                    _self.applyData.reimburserank_gas_price=0;
                }
                if(_self.applyData.trapply_vehicle_distance === '' || _self.applyData.trapply_vehicle_distance === undefined){
                    _self.applyData.trapply_vehicle_distance=0;
                }
            }else if(trapply_trans_way ==='1'){
                $('#trapply_vehicle_apply').val('').trigger('change');
                _self.vehicleDisable=true;
                _self.gasDisable=true;
                _self.toolsDisable=false;
                _self.applyData.trapply_vehicle_distance=0;
            } else if (trapply_trans_way ==='4') {
                $('#trapply_vehicle_apply').val('').trigger('change');
                $('#trapply_traffic_tools').val('').trigger('change');
                _self.vehicleDisable=true;
                _self.gasDisable=true;
                _self.toolsDisable=true;
                _self.applyData.trapply_vehicle_distance=0;
            }
        },
        //设置默认随行人数
        setTrapplyRCrewNum:function () {
            let _self=this;
            let trapply_reception_review_level= common.getSelect2Val('trapply_reception_review_level');
            if(trapply_reception_review_level){
                if(_self.applyData.trapply_reception_crew_num==='' || _self.applyData.trapply_reception_crew_num===null){
                    _self.applyData.trapply_reception_crew_num=1;
                }

                if(_self.applyData.trapply_reception_room_num==='' || _self.applyData.trapply_reception_room_num===null){
                    _self.applyData.trapply_reception_room_num=1;
                }
            }
        },
        //格式化陪同人员IDs
        formateUserIds:function (data) {
            let _self=this;
            if(data){
                let userIdsString='';
                let userArr=data.split(',');
                for(let j in userArr){
                    for(let i in _self.initData.userArr){
                        if(userArr[j]==_self.initData.userArr[i].id){
                            if(j==userArr.length-1){
                                userIdsString=userIdsString+_self.initData.userArr[i].text
                            }else {
                                userIdsString=userIdsString+_self.initData.userArr[i].text+','
                            }

                        }
                    }
                }
                $('#trapply_recetion_crew_ids').val(userIdsString)
            }
        },
        choiceGroup:function () {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback : async function (users){
            let _self = this;
            try {
                let name ='',userId='';
                _self.applyData.trapply_recetion_crew_names=[];
                let currentUser = common.getStoreData('userinfo');
                if(users.length==0){
                    $('#trapply_recetion_crew_ids').val('')
                    return
                }

                for(let i in users){
                    if(i!=users.length-1){
                        name=name+users[i].name+',';
                        userId=userId+users[i].user_id+',';
                    }else {
                        name=name+users[i].name
                        userId=userId+users[i].user_id;
                    }
                }
                _self.applyData.trapply_recetion_crew_ids=userId;
                $('#trapply_recetion_crew_ids').val(name)

            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
    .timeline {
        list-style-type: none;
        display: flex;
        align-items: center;
        justify-content: center;
        background: white;
    }

    .timeline:before {
        background: white;
    }

    .li {
        transition: all 200ms ease-in;
    }

    .timestamp {
        margin-bottom: 20px;
        padding: 0px 40px;
        display: flex;
        flex-direction: column;
        align-items: center;
        font-weight: 100;
    }

    .status {
        padding: 0px 20px;
        display: flex;
        justify-content: center;
        border-top: 2px solid #D6DCE0;
        position: relative;
        transition: all 200ms ease-in;
    }

    .status h4 {
        font-weight: 400;
        font-size: 16px;
    }

    .status:before {
        content: "";
        width: 25px;
        height: 25px;
        background-color: white;
        border-radius: 25px;
        border: 1px solid #ddd;
        position: absolute;
        top: -15px;
        left: 42%;
        transition: all 200ms ease-in;
    }

    .li {
        margin-top: 0px;
    }

    .li.complete .status {
        border-top: 2px solid #66DC71;
    }

    .li.complete .status:before {
        background-color: #66DC71;
        border: none;
        transition: all 200ms ease-in;
    }

    .li.complete .status h4 {
        color: #66DC71;
    }
</style>
