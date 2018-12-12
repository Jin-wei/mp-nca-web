<template>
    <div>
        <!-- begin breadcrumb -->
        <ol class="breadcrumb">
            <li><a href="/nca/homepage/NCAHomePageControl">首页</a></li>
            <li><a href="javascript:;">订单管理</a></li>
            <li class="active">管理配置</li>
        </ol>

        <div class="row">
            <div class="col-md-12">
                <div class="panel panel-inverse">
                    <ul class="nav nav-tabs">
                        <li v-for="(item, index) in tabArr" v-bind:class="index==0 ? 'active':''" @click="showTable(item.id)">
                            <a v-bind:href="'#type-tab' + item.id" data-toggle="tab">
                                <span>{{item.text}}</span>
                            </a>
                        </li>
                    </ul>
                    <div class="tab-content padding-lr-15">
                        <div v-bind:class="index==0 ? 'tab-pane fade active in' : 'tab-pane fade'" v-bind:id="'type-tab' + item.id" v-for="(item, index) in tabArr">
                            <div class="panel-heading-btn">
                                <button id="addM" class="btn btn-info pull-right" v-on:click="addRequireClick(item.id)" style="margin: 10px 0px">
                                   {{tabTitle[typeId]}}
                                </button>
                                <!--<button id="addM" class="btn btn-info" v-on:click="addRequireClick(item.id)" v-if="typeId == '2'">
                                    <i class="glyphicon glyphicon-plus"></i> 新增3D效果要求
                                </button>
                                <button id="addM" class="btn btn-info" v-on:click="addRequireClick(item.id)" v-if="typeId == '3'">
                                    <i class="glyphicon glyphicon-plus"></i> 新增内部审核要求
                                </button>
                                <button id="addM" class="btn btn-info" v-on:click="addRequireClick(item.id)" v-if="typeId == '4'">
                                    <i class="glyphicon glyphicon-plus"></i> 新增评审要求
                                </button>-->
                            </div>
                            <table v-bind:id="'table'+item.id"></table>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <div class="modal fade" id="AddModal">
            <div class="modal-dialog">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                        <h4 class="modal-title">{{tabTitle[typeId]}}</h4>
                        <!--<h4 class="modal-title" v-if="typeId == '1'">新增设计文档要求</h4>-->
                        <!--<h4 class="modal-title" v-if="typeId == '2'">新增3D效果要求</h4>-->
                        <!--<h4 class="modal-title" v-if="typeId == '3'">新增内部审核要求</h4>-->
                        <!--<h4 class="modal-title" v-if="typeId == '4'">新增评审要求</h4>-->
                    </div>
                    <div class="modal-body">
                        <div class="row">
                            <div class="form-group col-xs-12">
                                <input class="form-control" v-model="addRequire.requireName" v-bind:placeholder="namePlace">
                            </div>
                            <div class="form-group col-xs-12">
                                <input class="form-control" v-if="typeId != '2'" v-model="addRequire.requireDescription" v-bind:placeholder="desPlace">
                            </div>
                            <div class="form-group col-xs-12">
                                <select class="form-control" v-model="addRequire.requireHidden" v-if="typeId == '1'">
                                    <option value="0">否(用户不可见)</option>
                                    <option value="1">是(用户可见)</option>
                                </select>
                            </div>
                            <div class="form-group col-xs-2 pull-right" style="text-align: right">
                                <button type="button" class="btn btn-info" v-if="typeId == 1 ||typeId == 2 ||typeId == 3" v-on:click="saveClick(typeId)">保存</button>
                                <button type="button" class="btn btn-info" v-else="typeId == 4" v-on:click="saveClick(typeId)">提交</button>
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
    const apiUrl = '/api/nca/ordermanage/NCAOrderRequireControl?method=';

    export default {
        data: function() {
            return {
                tabTitle: {
                    1: '增加设计文档要求',
                    2: '增加3D效果要求',
                    3: '增加内部审核要求',
                    4: '增加评审要求',
                    5: '增加产品规划评审要求'
                },
                workRow: {},
                oldRow: '',
                pagePara: {},
                tabArr:[],
                typeId:'',
                addRequire:{},
                namePlace: '请输入文档名称',
                desPlace: '请输入文档内容描述'
            }
        },
        name: 'purchaseApplyDetailControl',
        async mounted() {
            await this.initData();
        },
        methods: {
            async initData() {
                let response = await this.$http.post(apiUrl + 'init', {});
                this.pagePara = response.data.info;
                this.tabArr = response.data.info.order_require_type;
                if (this.tabArr.length > 0) {
                    this.typeId = this.tabArr[0].id;
                }
                window.setTimeout(() => {
                    for(let i = 0;i < this.tabArr.length; i++){
                        this.initTable(this.tabArr[i].id);
                    }
                    common.reSizeCall();
                },100)

            },
            initTable(id) {
                window.tableEvents = {
                    'click .tableDelete': (e, value, row, index) => {
                        common.dealConfrimCommon('您确定删除么', () => {
                            console.log(row.require_id)
                            this.$http.post(apiUrl + 'delete_require', {requireId: row.require_id}).then((response) => {
                                let tableId = '#table'+row.type_id;
                                let $table = $(tableId);
                                $table.bootstrapTable("refresh");
                            }, (response) => {
                            });
                        });

                    }

                };

                let tableId = '#table'+id;
                let $table = $(tableId);
                let colums = [];
                if (id === '1') {
                    colums = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '文档名称'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                        common.BTRowFormatWithFormatter('require_hidden', '客户可见', (value, row) => {
                            return row.require_hidden === '0' ? '否' : '是';
                        }),
                    ]
                } else if (id === '2'){
                    colums = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '效果图名称'),
                    ]
                } else if (id === '3'){
                    colums = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '审核文档'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '文档内容描述',common.remarkFormatter),
                    ]
                } else if (id === '4') {
                    colums = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '评审项目'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '评审责任描述',common.remarkFormatter),
                    ]
                } else if (id === '5') {
                    colums = [
                        common.BTRowFormatWithIndex('NO.'),
                        common.BTRowFormatAlignLeft('require_name', '评审项目'),
                        common.BTRowFormatWithFormatterAlignLeft('require_description', '评审责任描述',common.remarkFormatter),
                        common.BTRowFormatEdSelectAlignLeft(this, 'require_user_id', '评审人', 'userInfo'),
                    ]
                }
                colums.push(common.BTRowFormatWithFE('', '操作', () => (`<a class="btn btn-info btn-xs m-r-5 tableDelete">删除</a>`), tableEvents));

                $table.bootstrapTable({
                    method: 'POST',
                    url: apiUrl + 'search_require',
                    queryParams: (params) => {
                        params.typeId = this.typeId;
                        params.domainId = common.getStoreData('userinfo').domain_id;
                        return JSON.stringify(params)
                    },
                    sidePagination: 'server',
                    ajaxOptions: common.bootstrapTableAjaxOtions,
                    responseHandler: function(res) {
                        return res.info;
                    },
                    height: common.getTableHeight(),
                    columns: colums,
                    idField: 'require_id',
                    uniqueId: 'require_id',
                    striped: true,
                    clickToSelect: true,
                    showRefresh: false,
                    pagination: true,
                    pageSize: common.pageSize(),
                    pageList: [10, 15, 25, 50, 100],
                    locale: 'zh-CN',
                    onEditableShown: (field, row, $el, editable) => {
                        this.oldRow = $.extend(true, {}, row);
                    },
                    onEditableSave: (field, row, oldValue, $el) => {
                        common.rowModifyWithT(this, apiUrl + 'modify_require', row, 'require_id', $table);
                    },
                    onPostBody: function() {
                        $('[data-toggle="popover"]').each(function () {
                            $(this).popover()
                        })
                    }
                });
                common.changeTableClass($table)
            },
            showTable(id) {
                let tableId = '#table'+id;
                let $table = $(tableId);
                this.typeId = id;
                $table.bootstrapTable("refresh");
            },
            addRequireClick(id) {
                this.addRequire = {};
                if (id === '1') {
                    this.namePlace = '请输入文档名称（必填）';
                    this.desPlace = '请输入文档内容描述';
                    this.addRequire.requireHidden = '1';
                } else if (id === '2'){
                    this.namePlace = '请输入效果图名称（必填）';
                } else if (id === '3'){
                    this.namePlace = '请输入审核文档名称（必填）';
                    this.desPlace = '请输入文档内容描述';
                } else if (id === '4'){
                    this.namePlace = '请输入评审项目名称（必填）';
                    this.desPlace = '请输入评审责任描述';
                } else if (id === '5'){
                    this.namePlace = '请输入评审项目名称（必填）';
                    this.desPlace = '请输入评审责任描述';
                }
                $("#AddModal").modal('show');
            },
            async saveClick(id) {
                if(this.addRequire.requireName == null || this.addRequire.requireName.length === 0){
                    if (id === '1') {
                        return common.dealPromptCommon('请输入文档名称')
                    } else if (id === '2'){
                        return common.dealPromptCommon('请输入效果图名称')
                    } else if (id === '3'){
                        return common.dealPromptCommon('请输入审核文档名称')
                    } else if (id === '4'){
                        return common.dealPromptCommon('请输入评审项目名称')
                    } else if (id === '5'){
                        return common.dealPromptCommon('请输入评审项目名称')
                    }
                }

                this.addRequire.typeId = this.typeId;
                this.addRequire.domainId = common.getStoreData('userinfo').domain_id;

                try {
                    await this.$http.post(apiUrl + 'add_require', this.addRequire);
                    common.dealSuccessCommon('添加成功');
                    let tableId = '#table'+this.typeId;
                    let $table = $(tableId);
                    $table.bootstrapTable("refresh");
                    $("#AddModal").modal('hide');
                } catch (error) {
                    common.dealErrorCommon(this, error)
                }
            }
        }
    }
</script>
<style scoped>
</style>
