<template>
<div>
    <!-- begin breadcrumb -->
    <ol class="breadcrumb">
        <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
        <li><a href="javascript: history.back()">地产商维护</a></li>
        <li class="active">地产商团队维护</li>
    </ol>
    <!-- end breadcrumb -->
    <div class="row">
        <div class="col-md-12">
            <div class="panel panel-inverse">
                <div class="panel-heading">
                    <div class="panel-heading-btn">
                        <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                    </div>
                    <h4 class="panel-title">地产商团队维护</h4>
                </div>
                <div class="panel-toolbar">
                    <div class="form-inline" role="form">
                        <div class="form-group">
                            <input class="form-control" v-model="searchText" placeholder="搜索姓名、电话或邮箱" style="width: 200px;">
                        </div>
                        <div class="form-group">
                            <button class="btn btn-info" v-on:click="search"><i class="fa fa-search"></i></button>
                        </div>
                        <div class="form-group  pull-right">
                            <button class="btn btn-info" v-on:click="addSignUser">增加
                      </button>
                        </div>
                    </div>
                </div>
                <div class="panel-body auto-height hidedesk" style="display:none;">
                    <table id="landAgentTeamTable"></table>
                </div>
            </div>
        </div>
    </div>

    <div class="modal fade" id="AddModal" data-backdrop="static">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                    <h4 class="modal-title">新增团队成员</h4>
                </div>

                <div class="modal-body">
                    <table class="table table-bordered">
                        <thead>
                        <tr>
                            <th></th>
                            <th>用户名</th>
                            <th>姓名</th>
                            <th>电话</th>
                            <th>邮箱</th>
                            <th>用户组</th>
                        </tr>
                        </thead>
                        <tbody>
                        <tr v-for="item in userList">
                            <td style="text-align: center">
                                <input type="checkbox" value="" v-model="item.checked"/>
                            </td>
                            <td>{{item.username}}</td>
                            <td>{{item.name}}</td>
                            <td style="text-align: center">{{item.phone}}</td>
                            <td>{{item.email}}</td>
                            <td style="text-align: center">{{item.usergroup_id|groupFormat}}</td>
                        </tr>
                        </tbody>
                    </table>
                </div>

                <div class="modal-footer">
                    <button class="btn btn-info" @click="onAddLandAgentUser">保存</button>
                </div>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc')
const apiUrl = '/api/nca/baseconfig/NCALandAgentControl?method='
import Constant from '../../../assets/js/constant'

export default {
    data: function() {
        return {
            pagePara: '',
            workRow: {},
            oldRow: '',
            suppliers:[],
            queryData: null,
            searchText: null,
            masterTable: null,
            modal: null,
            userList: []
        }
    },
    name: 'landAgentTeamControl',
    filters: {
        groupFormat: function (value) {
            let userGroup = Constant.getData('userGroup');
            for (let i = 0; i < userGroup.length; i++) {
                if (userGroup[i].id == value) {
                    return userGroup[i].text;
                }
            }
        }
    },
    mounted: function() {
        this.queryData = this.$route.query;
        console.log('queryData:', this.queryData);
        this.masterTable = $('#landAgentTeamTable')
        this.modal = $('#AddModal')
        this.initPage()
    },
    methods: {
        queryParams: function (params) {
            params.search_text = this.searchText;
            params.landagent_id = this.queryData.landagent_id;
            return JSON.stringify(params)
        },
        search: function(event) {
            this.masterTable.bootstrapTable("refresh", this.queryParams)
        },
        addSignUser: function() {
            this.$http.post(apiUrl + 'getUserForLandAgent', this.workRow).then(response => {
                this.userList = response.data.info.rows;
                this.workRow = {}
                this.modal.modal('show');
            });
        },
        initTable: function () {
            window.tableEvents = {
                'click .tableDelete': (e, value, row, index) => {
                    common.rowDeleteWithApi(this, '地产商用户删除', apiUrl + 'removeSignUser', this.masterTable, row, 'thirdsignuser_id')
                }
            };
            this.masterTable.bootstrapTable({
                method: 'POST',
                url: apiUrl + 'getSignUser',
                queryParams: this.queryParams,
                sidePagination: 'server',
                ajaxOptions: common.bootstrapTableAjaxOtions,
                responseHandler: (res) => {
                    return res.info;
                },
                height: common.getTableHeight(),
                columns: [
                    common.BTRowFormatWithIndex('NO.'),
                    common.BTRowFormatAlignLeft('username', '用户名'),
                    common.BTRowFormatAlignLeft('name', '姓名'),
                    common.BTRowFormat('phone', '电话'),
                    common.BTRowFormatAlignLeft('email', '邮箱'),
                    common.BTRowFormatEdSelect2Disabled(this, 'usergroup_id', '用户组', 'userGroup'),
                    common.actFormatter('act', `<a class="btn btn-xs btn-info tableDelete">删除</a>`, tableEvents)
                ],
                idField: 'thirdsignuser_id',
                uniqueId: 'thirdsignuser_id',
                striped: true,
                clickToSelect: true,
                pagination: true,
                pageSize: common.pageSize(),
                pageList: [10, 15, 25, 50, 100],
                locale: 'zh-CN'
            })
            common.changeTableClass(this.masterTable)
        },
        initPage: async function() {
            let response = await this.$http.post(apiUrl + 'init', {})
            this.pagePara = response.data.info
            this.initTable()
            common.reSizeCall()
            console.log('init success')
        },
        onAddLandAgentUser: function () {
            let userArray = [];
            for (let i = 0; i < this.userList.length; i++) {
                let item = this.userList[i];
                if (item.checked) {
                    userArray.push(item.user_id);
                }
            }
            console.log('userArray:', userArray);

            this.$http.post(apiUrl + 'signUserForLandAgent', {
                landAgentId: this.queryData.landagent_id,
                userArray: userArray
            }).then(response => {
                this.masterTable.bootstrapTable("refresh");
                this.modal.modal('hide');
            });
        }
    }
}
</script>
<style scoped>

    .modal-body thead th{
        text-align: center;
    }
</style>
