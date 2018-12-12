<template>
<div>
    <div class="panel panel-inverse">
        <div class="panel-heading">
            <div class="panel-heading-btn">
                <button class="btn btn-info btn-xs" v-on:click="teamM">修改</button>
            </div>
            <h4 class="panel-title">团队管理</h4>
        </div>
        <div class="panel-body">
            <table id="teamTable"></table>
        </div>
    </div>
    <userSelectDialog :usergroupId="usergroupId" :users="users"></userSelectDialog>
</div>
</template>
<script>
import Vue from 'vue'
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAEstateControl?method=';

import userSelectDialog from '../../../../components/common/userSelectDialog.vue'

export default {
    props: ['estate', 'pagePara'],
    data: function() {
        return {
            usergroupId: '',
            users: [],
            eventHub: new Vue()
        }
    },
    name: 'NCAEDRoomTeamControl',
    components: {
        userSelectDialog
    },
    mounted: function() {
        let _self = this;
        let $teamTable = $('#teamTable')

        $teamTable.bootstrapTable({
            columns: [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatAlignLeft('name', '姓名'),
                common.BTRowFormatEdSelect2Disabled(_self, 'gender', '性别', 'genderInfo'),
                common.BTRowFormat('phone', '手机'),
                common.BTRowFormatAlignLeft('email', '邮箱'),
                common.BTRowFormat('position', '职位')
            ],
            idField: 'user_id',
            uniqueId: 'user_id',
            formatNoMatches: function() {
                return
            }
        });
    },
    watch: {
        estate: async function() {
            let _self = this;
            try {
                let response = await _self.$http.post(apiUrl + 'queryTeam', {
                    estate_id: _self.estate.estate_id
                });
                _self.users = JSON.parse(JSON.stringify(response.data.info))
                $('#teamTable').bootstrapTable('load', {
                    data: _self.users
                })

            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    },
    methods: {
        teamM: function(event) {
            let _self = this;
            _self.eventHub.$emit('show-userselect-dialog');
        },
        userSelectCallback: async function(users) {
            let _self = this;
            try {
                let response = await _self.$http.post(apiUrl + 'changeTeam', {
                    estate_id: _self.estate.estate_id,
                    users: users
                });
                _self.$parent.refreshEstate();

            } catch (error) {
                common.dealErrorCommon(_self, error);
            }
        }
    }
}
</script>
<style scoped>
</style>
