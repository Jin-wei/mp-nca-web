<template>
<div class="modal fade" id="userDepartment">
    <div class="modal-dialog" style="width:700px;">
        <div class="modal-content">
            <div class="modal-header">
                <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
                <h4 class="modal-title">选择部门</h4>
            </div>
            <div class="modal-body row">
                <div class="col-md-3 modal-height">
                    <ul id="selectUserDepartmentTree" class="ztree"></ul>
                </div>
                <div class="col-md-9">
                    <div class="col-md-5 modal-height">
                        <table id="gUserDepartmentTable"></table>
                    </div>
                    <div class="col-md-1 modal-height vertical-container" v-show="mulityFlag2!=1">
                        <div class="btn-group-vertical">
                            <button type="button" class="btn btn-primary btn-icon" @click="addusers"><i class="icon-arrow-right"></i></button>
                        </div>
                    </div>
                    <div class="col-md-5 modal-height" v-show="mulityFlag2!=1">
                        <table id="rUserDepartmentTable"></table>
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
const apiUrl = '/api/nca/baseconfig/UserDepartmentControl?method=';

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
    name: 'userNewDepartment',
    created: function() {
        let _self = this
        _self.$parent.eventHub.$on('show-departmentselect-dialog', () => {
            _self.showDialog()
        })
    },
    mounted: function() {
        let _self = this;
        let $gUserTable = $('#gUserDepartmentTable')
        let $rUserTable = $('#rUserDepartmentTable')
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
                common.BTRowFormatEditableLeft('department_name', '部门')
            ],
            idField: 'department_id',
            uniqueId: 'department_id',
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
                common.BTRowFormatEditableLeft('department_name', '部门'),
                common.actFormatter('act', removeFormatter, userGroupEvents)
            ],
            idField: 'department_id',
            uniqueId: 'department_id',
            clickToSelect: true,
            formatNoMatches: function() {
                return
            }
        });
    },
    methods: {
        showDialog: async function(event) {
            let _self = this;
            let $gUserTable = $('#gUserDepartmentTable')
            let $rUserTable = $('#rUserDepartmentTable')

            async function zTreeOnClick(event, treeId, treeNode) {

                try {
                    let response = await _self.$http.post(apiUrl + 'search_user', {
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
                    let response = await _self.$http.post(apiUrl + 'search_g', {});
                    let zNodes = response.data.info;
                    $.fn.zTree.destroy("selectUserDepartmentTree");
                    let treeObj = $.fn.zTree.init($("#selectUserDepartmentTree"), {
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
            $('#userDepartment').modal('show')
        },
        addusers: function(event) {
            let _self = this,
                checkUsers = [],
                resultUsers = [],
                addUsers = [];
            let $gUserTable = $('#gUserDepartmentTable')
            let $rUserTable = $('#rUserDepartmentTable')
            checkUsers = $gUserTable.bootstrapTable('getAllSelections')
            resultUsers = $rUserTable.bootstrapTable('getData')
            for (let i = 0; i < checkUsers.length; i++) {
                let addFlag = true
                for (let j = 0; j < resultUsers.length; j++) {
                    if (checkUsers[i].department_id === resultUsers[j].department_id) {
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
                let $gUserTable = $('#gUserDepartmentTable')
                let $rUserTable = $('#rUserDepartmentTable')
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
                $('#userDepartment').modal('hide')
                _self.$parent.departmentSelectCallback($('#rUserDepartmentTable').bootstrapTable('getData'))
            } else {
                $('#userDepartment').modal('hide')
                _self.$parent.departmentSelectCallback($('#rUserDepartmentTable').bootstrapTable('getData'))
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
