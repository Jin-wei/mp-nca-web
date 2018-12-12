<template>
    <div>
        <!-- begin breadcrumb -->
        <div class="row">
            <div class="col-md-12">
                <form @submit.prevent="addAct">
                    <div class="panel panel-inverse">
                        <div class="panel-heading">
                            <div class="panel-heading-btn">
                                <button type="submit" v-show="meeting_remark_state !=1 " class="btn btn-info btn-xs">保存</button>
                                <a href="javascript:;" class="btn btn-xs btn-icon btn-circle btn-default" data-click="panel-expand"><i class="fa fa-expand"></i></a>
                            </div>
                            <h4 class="panel-title">会议记录详情</h4>
                        </div>
                        <div class="panel-body">
                            <textarea class="form-control" id="meeting_remark" style="width: 100%;height:300px;" data-parsley-required="true" data-parsley-maxlength="100"></textarea>
                        </div>
                    </div>
                </form>
            </div>
        </div>

    </div>
</template>
<script>
const common = require('commonFunc');
const apiUrl = '/api/nca/baseconfig/NCAMeetingMinuteControl?method=';

export default {
    props: ['meeting', 'pagePara'],
    data: function() {
        return {
            workRow: {},
            meeting_remark_state: ''
        }
    },
    name: 'NCAMinutesInformationControl',
    watch: {
        pagePara: function() {
            let _self = this;
        },
        meeting: function() {
            let _self = this;
            _self.meeting_remark_state = _self.meeting.meeting_remark_state
            $('#meeting_remark').val(_self.meeting.meeting_remark);
        }
    },
    methods: {
        addAct: async function(event) {
            let _self = this;
            _self.workRow.meeting_id = _self.meeting.meeting_id;
            _self.workRow.meeting_remark = $('#meeting_remark').val();
            _self.workRow.flag = 1;

            _self.$http.post(apiUrl + 'add_m', _self.workRow).then((response)=>{
                let retData = response.data.info;
                _self.workRow = {};
                common.dealSuccessCommon('已保存');
            }, (error)=>{
                common.dealErrorCommon(_self, error)
            });

        }
    }
}
</script>
<style scoped>
</style>
