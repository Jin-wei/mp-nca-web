<template>
<div>
    <ul class="timeline">
        <li id="li_new" class="li complete">
            <div class="timestamp">
                <span class="">新建</span>
            </div>
            <div class="status">
                <h4>{{state_time[0]}}</h4>
            </div>
        </li>

        <li id="li_designing" class="li">
            <div class="timestamp">
                <span class="">设计中</span>
            </div>
            <div class="status">
                <h4>{{state_time[1]}}</h4>
            </div>
        </li>
        <li id="li_checking" class="li ">
            <div class="timestamp">
                <span class="">审核中</span>
            </div>
            <div class="status">
                <h4>{{state_time[2]}}</h4>
            </div>
        </li>
        <li id="li_signed" class="li ">
            <div class="timestamp">
                <span class="">签约中</span>
            </div>
            <div class="status">
                <h4>{{state_time[3]}}</h4>
            </div>
        </li>
        <li id="li_reviewing" class="li ">
            <div class="timestamp">
                <span class="">评审中</span>
            </div>
            <div class="status">
                <h4>{{state_time[4]}}</h4>
            </div>
        </li>
        <li id="li_working" class="li ">
            <div class="timestamp">
                <span class="">施工中</span>
            </div>
            <div class="status">
                <h4>{{state_time[6]}}</h4>
            </div>
        </li>
        <li id="li_finish" class="li">
            <div class="timestamp">
                <span class="">已完成</span>
            </div>
            <div class="status">
                <h4>{{state_time[7]}}</h4>
            </div>
        </li>
    </ul>
</div>
</template>

<script>
const common = require('commonFunc');
export default {
    props: ['order'],
    data: function() {
        return {
            state_time: []
        }
    },
    mounted: function() {
        let _self = this;

    },
    watch: {
        order: function() {
            let _self = this;

            function initTimeLine() {
                if (_self.order.statedict.NEW)
                    $("#li_new").addClass("complete");
                if (_self.order.statedict.DESIGNING)
                    $("#li_designing").addClass("complete");
                if (_self.order.statedict.CHECKING)
                    $("#li_checking").addClass("complete");
                if (_self.order.statedict.SIGNED)
                    $("#li_signed").addClass("complete");
                if (_self.order.statedict.REVIEWING)
                    $("#li_reviewing").addClass("complete");
                if (_self.order.statedict.WORKING)
                    $("#li_working").addClass("complete");
                if (_self.order.statedict.FINISHI)
                    $("#li_finish").addClass("complete");
                let state_time = []
                state_time.push(_self.order.statedict.NEW || '-');
                state_time.push(_self.order.statedict.DESIGNING || ' -');
                state_time.push(_self.order.statedict.CHECKING || ' -');
                state_time.push(_self.order.statedict.SIGNED || ' -');
                state_time.push(_self.order.statedict.REVIEWING || ' -');
                state_time.push(_self.order.statedict.SIGNED || ' -');
                state_time.push(_self.order.statedict.WORKING || ' -');
                state_time.push(_self.order.statedict.FINISHI || ' -');
                _self.state_time = state_time
            }

            initTimeLine();

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
