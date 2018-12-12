<!--户型详情-->
<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript:;" @click="back">户型及房号详情</a></li>
        <li class="active">楼盘户型详情</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12 col-sm-12">
            <div class="widget widget-stats bg-black-lighter">
                <div class="stats-info">
                    <h4>基本数据</h4>
                    <div class="row">
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">户型</span>
                                <input class="form-control" v-model="_self.roomType.roomtype_spec_name" disabled>
                            </div>
                        </div>
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">户型类别</span>
                                <input class="form-control" id="house_type_name" disabled>
                            </div>
                        </div>
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">建筑面积</span>
                                <input class="form-control" v-model="_self.roomType.roomtype_srcage" disabled>
                            </div>
                        </div>
                        <div class="form-group col-sm-3">
                            <div class="input-group">
                                <span class="input-group-addon">描述</span>
                                <input class="form-control" v-model="roomType.describe" disabled>
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
                <li class="active" @click="setData('RTDesignControlOD', $event)">
                    <a href="#RTDesignControl" data-toggle="tab">
      								<span class="visible-xs">设计图</span>
      								<span class="hidden-xs">设计图</span>
      							</a>
                </li>
                <li class="" @click="setData('RTSpaceControlOD', $event)">
                    <a href="#RTSpaceControl" data-toggle="tab">
      								<span class="visible-xs">空间列表</span>
      								<span class="hidden-xs">空间列表</span>
      							</a>
                </li>
                <li class="" @click="setData('RTMaterialControlOD', $event)">
                    <a href="#RTMaterialControl" data-toggle="tab">
      								<span class="visible-xs">物料清单</span>
      								<span class="hidden-xs">物料清单</span>
      							</a>
                </li>
                <li class="" @click="setData('RTProcessControlOD', $event)">
                    <a href="#RTProcessControl" data-toggle="tab">
      								<span class="visible-xs">施工工序</span>
      								<span class="hidden-xs">施工工序</span>
      							</a>
                </li>
                <li class="" @click="setData('RTAcceptanceControlOD', $event)">
                    <a href="#RTAcceptanceControl" data-toggle="tab">
      								<span class="visible-xs">验收项</span>
      								<span class="hidden-xs">验收项</span>
      							</a>
                </li>
            </ul>

            <div class="tab-content">
                <div class="tab-pane fade active in" id="RTDesignControl">
                    <designControl :roomType="RTDesignControlOD" :pagePara="pagePara"></designControl>
                </div>
                <div class="tab-pane fade" id="RTSpaceControl">
                    <spaceControl :roomType="RTSpaceControlOD" :pagePara="pagePara"></spaceControl>
                </div>
                <div class="tab-pane fade" id="RTMaterialControl">
                    <materialControl :roomType="RTMaterialControlOD" :pagePara="pagePara"></materialControl>
                </div>
                <div class="tab-pane fade" id="RTProcessControl">
                    <processControl :roomType="RTProcessControlOD" :pagePara="pagePara"></processControl>
                </div>
                <div class="tab-pane fade" id="RTAcceptanceControl">
                    <acceptanceControl :roomType="RTAcceptanceControlOD" :pagePara="pagePara"></acceptanceControl>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCARoomTypeDetailControl?method=';

import designControl from './NCARTDesignHQControl.vue'
import spaceControl from './NCARTSpaceHQControl.vue'
import materialControl from './NCARTMaterialHQControl.vue'
import processControl from './NCARTProcessHQControl.vue'
import acceptanceControl from './NCARTAcceptanceHQControl.vue'

export default {
    data: function() {
        return {
            roomType: {},
            tabInfo: '',
            pagePara: {},
            RTDesignControlOD: {},
            RTSpaceControlOD: {},
            RTMaterialControlOD: {},
            RTProcessControlOD: {},
            RTAcceptanceControlOD: {}
        }
    },
    components: {
        designControl,
        spaceControl,
        materialControl,
        processControl,
        acceptanceControl
    },
    name: 'NCARoomTypeDetailControl',
    mounted: async function() {
        let _self = this;

        //获取常量数据
        async function getInit() {
            try {
                let roomTypeId = common.getUrlParams("roomTypeId");
                let param = {
                    roomtype_id: roomTypeId
                };
                let response = await _self.$http.post(apiUrl + 'init', param);
                _self.pagePara = $.extend(true, {}, response.data.info);
                console.log(_self.pagePara.houseTypeInfo,'_self.pagePara.houseTypeInfo')
                console.log(_self,'_self')
                for (let i = 0; i < _self.pagePara.houseTypeInfo.length; i++) {
                    if (_self.roomType.type === _self.pagePara.houseTypeInfo[i].id) {
                        $('#house_type_name').val(_self.pagePara.houseTypeInfo[i].text)
                    }
                }
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }

//        //获取户型详情
//        async function getRoomType() {
//            let roomTypeId = common.getUrlParams("roomTypeId");
//            let param = {
//                roomtype_id: roomTypeId
//            };
//            try {
//                let response = await _self.$http.post(apiUrl + 'get_roomType', param);
//                _self.roomType = response.data.info;
//            } catch (error) {
//                common.dealErrorCommon(_self, error)
//            }
//        }
        _self.getRoomType();
        await getInit();
    },
    methods: {
        back: function() {
            let _self = this;
            let url = '/nca/baseconfig/NCAEstateDetailControl'
            this.$router.push({
                path: url,
                query: {
                    estateId: _self.roomType.estate_id
                }
            })
        },
        updateRoomType: async function () {
          let _self = this
          let roomTypeId = common.getUrlParams("roomTypeId");
          let param = {
            roomtype_id: roomTypeId
          };
          try {
            let response = await _self.$http.post(apiUrl + 'get_roomType', param);
            _self.roomType = response.data.info
            _self.setData(_self.tabInfo)
          } catch (error) {
            common.dealErrorCommon(_self, error)
          }
        },
        setData: async function(tabData, event) {
            let _self = this;
            await _self.getRoomType();
            _self.tabInfo = tabData;
            _self[tabData] = JSON.parse(JSON.stringify(_self.roomType));
        },
        getRoomType:async function(){
            let _self = this;
            let roomTypeId = common.getUrlParams("roomTypeId");
            let param = {
                roomtype_id: roomTypeId
            };
            try {
                let response = await _self.$http.post(apiUrl + 'get_roomType', param);
                _self.roomType = response.data.info;
                console.log(_self.roomType,'roomType')
            } catch (error) {
                common.dealErrorCommon(_self, error)
            }
        }
    }
}
</script>
<style scoped>
</style>
