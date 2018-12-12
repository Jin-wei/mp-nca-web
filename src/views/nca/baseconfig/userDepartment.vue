<template>
<div class="modal fade" id="userGroup">
    <div class="modal-dialog" style="width:700px;">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                <h4 class="modal-title">选择角色</h4>
            </div>
            <div class="modal-body row">
                <div class="col-md-3 modal-height">
                    <ul id="selectUserTree" class="ztree"></ul>
                </div>
                <div class="col-md-9">
                    <div class="col-md-5 modal-height">
                        <table id="gUserTable"></table>
                    </div>
                    <div class="col-md-1 modal-height vertical-container" v-show="mulityFlag2!=1">
                        <div class="btn-group-vertical">
                            <button type="button" class="btn btn-primary btn-icon" @click="addusers"><i class="icon-arrow-right"></i></button>
                        </div>
                    </div>
                    <div class="col-md-5 modal-height" v-show="mulityFlag2!=1">
                        <table id="rUserTable"></table>
                    </div>
                </div>
            </div>
            <div class="modal-footer">
                <a href="javascript:;" class="btn btn-sm btn-white" data-dismiss="modal">关闭</a>
                <a @click="callBack" class="btn btn-sm btn-primary">确定</a>
            </div>
        </div>
    </div>
</div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAHumanResourceControl?method=';

export default {
    props: {
        'usergroupId': String,
        'mulityFlag': String,
        'users': [Object, Array]
    },
    data: function() {
        return {
            mulityFlag2: '2'
        }
    },
    name: 'userDepartment',
    created: function() {
        let _self = this
        _self.$parent.eventHub.$on('show-userselect-dialog', () => {
            _self.showDialog()
        })
    },
    mounted: function() {
        let _self = this;
        let $gUserTable = $('#gUserTable')
        let $rUserTable = $('#rUserTable')
        _self.mulityFlag2 = _self.mulityFlag

        window.userGroupEvents = {
            'click .userSelectDelete': function(e, value, row, index) {
                $rUserTable.bootstrapTable('remove', {
                    field: 'usergroup_id',
                    values: [row.usergroup_id]
                })
            }
        }

        let tf = false
        if(_self.mulityFlag2=='1'){
            tf = true
        } else {
            tf = false
        }

        $gUserTable.bootstrapTable({
            height: 370,
            columns: [{
                    field: 'state',
                    checkbox: true
                },
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEditableLeft('usergroup_name', '部门')
            ],
            idField: 'usergroup_id',
            uniqueId: 'usergroup_id',
            clickToSelect: true,
            singleSelect: tf,
            formatNoMatches: function() {
                return
            }
        });

        function removeFormatter(value, row, index) {
            return [
                '<a class="userSelectDelete" title="删除">',
                '<i class="glyphicon glyphicon-remove"></i>',
                '</a>',
            ].join('')
        }

        $rUserTable.bootstrapTable({
            height: 370,
            columns: [
                common.BTRowFormatWithIndex('NO.'),
                common.BTRowFormatEditableLeft('usergroup_name', '部门'),
                common.actFormatter('act', removeFormatter, userGroupEvents)
            ],
            idField: 'usergroup_id',
            uniqueId: 'usergroup_id',
            clickToSelect: true,
            formatNoMatches: function() {
                return
            }
        });
    },
    methods: {
        showDialog: async function(event) {
            let _self = this;
            let $gUserTable = $('#gUserTable')
            let $rUserTable = $('#rUserTable')

            async function zTreeOnClick(event, treeId, treeNode) {
                if (treeNode.node_type === '00') {
                    try {
                        let response = await _self.$http.post(apiUrl + 'search_user', {
                            usergroup_id: treeNode.usergroup_id
                        });
                        $gUserTable.bootstrapTable('load', {
                            data: response.data.info,
                        })
                    } catch (error) {
                        common.dealErrorCommon(_self, error);
                    }
                }
            };
            async function getTreeData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'search_g', {});
                    let zNodes = response.data.info;
                    $.fn.zTree.destroy("selectUserTree");
                    let treeObj = $.fn.zTree.init($("#selectUserTree"), {
                        callback: {
                            onClick: zTreeOnClick
                        }
                    }, zNodes);
                    treeObj.expandAll(true);
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            }

            $rUserTable.bootstrapTable('load', {
                data: _self.users
            })
            console.log('data',_self.users)
            $gUserTable.bootstrapTable('removeAll')
            await getTreeData()
            $('#userGroup').modal('show')
        },
        addusers: function(event) {
            let _self = this,
                checkUsers = [],
                resultUsers = [],
                addUsers = [];
            let $gUserTable = $('#gUserTable')
            let $rUserTable = $('#rUserTable')
            checkUsers = $gUserTable.bootstrapTable('getAllSelections')
            resultUsers = $rUserTable.bootstrapTable('getData')
            for (let i = 0; i < checkUsers.length; i++) {
                let addFlag = true
                for (let j = 0; j < resultUsers.length; j++) {
                    if (checkUsers[i].usergroup_id === resultUsers[j].usergroup_id) {
                        addFlag = false
                        break
                    }
                }
                if (addFlag) {
                    addUsers.push(checkUsers[i])
                }

            }

            $rUserTable.bootstrapTable('append', addUsers);
        },
        callBack: function(event) {
            let _self = this;
            if (_self.mulityFlag2=='1') {
                let _self = this,
                    checkUsers = [],
                    resultUsers = [],
                    addUsers = [];
                let $gUserTable = $('#gUserTable')
                let $rUserTable = $('#rUserTable')
                checkUsers = $gUserTable.bootstrapTable('getAllSelections')
                resultUsers = $rUserTable.bootstrapTable('getData')
                for (let i = 0; i < checkUsers.length; i++) {
                    let addFlag = true
                    for (let j = 0; j < resultUsers.length; j++) {
                        if (checkUsers[i].user_id === resultUsers[j].user_id) {
                            addFlag = false
                            break
                        }
                    }
                    if (addFlag) {
                        addUsers.push(checkUsers[i])
                    }

                }

                $rUserTable.bootstrapTable('append', addUsers);
                $('#userGroup').modal('hide')
                _self.$parent.userSelectCallback2($('#rUserTable').bootstrapTable('getData'))
            } else {
                $('#userGroup').modal('hide')
                _self.$parent.userSelectCallback2($('#rUserTable').bootstrapTable('getData'))
            }
        }
    }
}
</script>
<style scoped>
.modal-height {
    height: 400px;
    overflow: auto;
}
</style>
