<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:history.back()">员工信息管理</a></li>
            <li class="active">员工信息管理详情</li>
        </ol>
        <!-- end breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <ul class="nav nav-tabs">
                    <li class="active" @click="setData('eEssentialInformation', $event)">
                        <a href="#EEssentialInformationControl" data-toggle="tab">
                            <span class="visible-xs">基本信息</span>
                            <span class="hidden-xs">基本信息</span>
                        </a>
                    </li>
                    <li class="" @click="setData('ePersonalInformation', $event)">
                        <a href="#EPersonalInformationControl" data-toggle="tab">
                            <span class="visible-xs">个人信息</span>
                            <span class="hidden-xs">个人信息</span>
                        </a>
                    </li>
                    <li class="" @click="setData('ePersonnelContract', $event)">
                        <a href="#EPersonnelContractControl" data-toggle="tab">
                            <span class="visible-xs">人事合同</span>
                            <span class="hidden-xs">人事合同</span>
                        </a>
                    </li>
                    <li class="" @click="setData('eWorkExperience', $event)">
                        <a href="#EWorkExperienceControl" data-toggle="tab">
                            <span class="visible-xs">工作经历</span>
                            <span class="hidden-xs">工作经历</span>
                        </a>
                    </li>
                    <li class="" @click="setData('kujialeContract', $event)">
                        <a href="#KujialeContractControl" data-toggle="tab">
                            <span class="visible-xs">酷家乐账号</span>
                            <span class="hidden-xs">酷家乐账号</span>
                        </a>
                    </li>
                </ul>

                <div class="tab-content">
                    <div class="tab-pane fade active in" id="EEssentialInformationControl">
                        <eEssentialInformationControl :employee="eEssentialInformation" :pagePara="pagePara"></eEssentialInformationControl>
                    </div>
                    <div class="tab-pane fade" id="EPersonalInformationControl">
                        <ePersonalInformationControl :employee="ePersonalInformation" :pagePara="pagePara"></ePersonalInformationControl>
                    </div>
                    <div class="tab-pane fade" id="EPersonnelContractControl">
                        <ePersonnelContractControl :employee="ePersonnelContract" :pagePara="pagePara"></ePersonnelContractControl>
                    </div>
                    <div class="tab-pane fade" id="EWorkExperienceControl">
                        <eWorkExperienceControl :employee="eWorkExperience" :pagePara="pagePara"></eWorkExperienceControl>
                    </div>
                    <div class="tab-pane fade" id="KujialeContractControl">
                        <kujialeContractControl :employee="kujialeContract" :pagePara="pagePara"></kujialeContractControl>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEmployeeInformationControl?method=';

import eEssentialInformationControl from './NCAEEssentialInformationControl.vue'
import ePersonalInformationControl from './NCAEPersonalInformationControl.vue'
import ePersonnelContractControl from './NCAEPersonnelContractControl.vue'
import eWorkExperienceControl from './NCAEWorkExperienceControl.vue'
import kujialeContractControl from './NCAKujialeContractControl.vue'

export default {
    data: function() {
        return {
            pagePara:{},
            employee: {},
            eEssentialInformation: {},
            ePersonalInformation: {},
            ePersonnelContract: {},
            eWorkExperience: {},
            kujialeContract: {},
            tabInfo: '',
            userContract: {}
        }
    },
    components: {
        eEssentialInformationControl,
        ePersonalInformationControl,
        ePersonnelContractControl,
        eWorkExperienceControl,
        kujialeContractControl
    },
    name: 'NCAEmployeeDetailControl',
    mounted: async function() {
        this.getEmployeeInformation();
        this.getEmployee();

    },
    watch: {
        employee: function() {
            if (this.employee && !this.tabInfo) {
                this.setData('eEssentialInformation');
            }
        }
    },
    methods: {
        getEmployee: async function () {
            let user_id = this.$route.query.user_id;
            let state = this.$route.query.state;
            let param = {
                user_id: user_id
            };

            try {
                let response = await this.$http.post(apiUrl + 'search_d', param);
                this.employee = response.data.info;
                this.userContract = this.employee.userContract.length
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        getEmployeeInformation: async function () {
            try {
                let response = await this.$http.post(apiUrl + 'init');
                this.pagePara = response.data.info;
            } catch (error) {
                common.dealErrorCommon(this, error)
            }
        },
        setData: async function(tabData, event) {
            await this.getEmployee();
            this.tabInfo = tabData;
            this[tabData] = this.employee;
        },
        refreshEmployee: function(tabData, event) {
            this.getEmployee()
        }
    }
}
</script>
<style scoped>
    textarea{
        width:100%;resize:none
    }
</style>
