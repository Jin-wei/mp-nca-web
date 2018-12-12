<template>
<div class="modal fade" id="userDepartmentGroup">
    <div class="modal-dialog" style="width:700px;">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                <h4 class="modal-title">选择部门</h4>
            </div>
            <div class="modal-body row">
                <div class="col-md-3 modal-height">
                    <ul id="selectUserDepartmentGroupTree" class="ztree"></ul>
                </div>
                <div class="col-md-9">
                    <div class="col-md-5 modal-height">
                        <table id="gUserDepartmentGroupTable"></table>
                    </div>
                    <div class="col-md-1 modal-height vertical-container" v-show="mulityFlag2!=1">
                        <div class="btn-group-vertical">
                            <button type="button" class="btn btn-primary btn-icon" @click="addusers"><i class="icon-arrow-right"></i></button>
                        </div>
                    </div>
                    <div class="col-md-5 modal-height" v-show="mulityFlag2!=1">
                        <table id="rUserDepartmentGroupTable"></table>
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
const apiUrl = '/api/nca/baseconfig/UserDepartmentGroupControl?method=';

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
    name: 'userDepartmentGroup',
    created: function() {
        let _self = this
        _self.$parent.eventHub.$on('show-departmentgroupselect-dialog', () => {
            _self.showDialog()
        })
    },
    mounted: function() {
        let _self = this;
        let $gUserTable = $('#gUserDepartmentGroupTable')
        let $rUserTable = $('#rUserDepartmentGroupTable')
        _self.mulityFlag2 = _self.mulityFlag

        window.userGroupEvents = {
            'click .userSelectDelete': function(e, value, row, index) {
                $rUserTable.bootstrapTable('remove', {
                    field: 'department_id',
                    values: [row.department_id]
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
                common.BTRowFormatEditableLeft('position_name', '岗位')
            ],
            idField: 'position_id',
            uniqueId: 'position_id',
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
                common.BTRowFormatEditableLeft('position_name', '岗位'),
                common.actFormatter('act', removeFormatter, userGroupEvents)
            ],
            idField: 'position_id',
            uniqueId: 'position_id',
            clickToSelect: true,
            formatNoMatches: function() {
                return
            }
        });
    },
    methods: {
        showDialog: async function(event) {
            let _self = this;
            let $gUserTable = $('#gUserDepartmentGroupTable')
            let $rUserTable = $('#rUserDepartmentGroupTable')

            async function zTreeOnClick(event, treeId, treeNode) {

                try {
                    let response = await _self.$http.post(apiUrl + 'newsearch_user', {
                        department_id: treeNode.department_id
                    });
                    $gUserTable.bootstrapTable('load', {
                        data: response.data.info,
                    })
                } catch (error) {
                    common.dealErrorCommon(_self, error);
                }
            };
            async function getTreeData() {
                try {
                    let response = await _self.$http.post(apiUrl + 'newsearch_g', {});
                    let zNodes = response.data.info;
                    $.fn.zTree.destroy("selectUserDepartmentGroupTree");
                    let treeObj = $.fn.zTree.init($("#selectUserDepartmentGroupTree"), {
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
            $gUserTable.bootstrapTable('removeAll')
            await getTreeData()
            $('#userDepartmentGroup').modal('show')
        },
        addusers: function(event) {
            let _self = this,
                checkUsers = [],
                resultUsers = [],
                addUsers = [];
            let $gUserTable = $('#gUserDepartmentGroupTable')
            let $rUserTable = $('#rUserDepartmentGroupTable')
            checkUsers = $gUserTable.bootstrapTable('getAllSelections')
            resultUsers = $rUserTable.bootstrapTable('getData')
            for (let i = 0; i < checkUsers.length; i++) {
                let addFlag = true
                for (let j = 0; j < resultUsers.length; j++) {
                    if (checkUsers[i].position_id === resultUsers[j].position_id) {
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
                let $gUserTable = $('#gUserDepartmentGroupTable')
                let $rUserTable = $('#rUserDepartmentGroupTable')
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
                $('#userDepartmentGroup').modal('hide')
                _self.$parent.departmentGroupSelectCallback($('#rUserDepartmentGroupTable').bootstrapTable('getData'))
            } else {
                $('#userDepartmentGroup').modal('hide')
                _self.$parent.departmentGroupSelectCallback($('#rUserDepartmentGroupTable').bootstrapTable('getData'))
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
