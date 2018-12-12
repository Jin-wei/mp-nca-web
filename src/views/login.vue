<template>
<div v-cloak>
</div>
</template>
<script>
const common = require('commonFunc')

export default {
    name: 'login',
    data: function() {
        return {
        }
    },
    mounted: function() {
        let _self = this
        _self.toLogin();
    },
    methods: {
        toLogin: function() {
            let _self=this;
            let ercUrl='101.201.152.171';

            if(document.domain===ercUrl){
                common.setStoreData('tenant','erc');
                _self.$router.push({
                    path:'/loginErc'
                });
            }else{
                common.setStoreData('tenant','nca');
                _self.$router.push({
                    path:'/loginNca'
                });
            }
        }
    }
}
</script>
