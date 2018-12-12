<template>
<div>
    <!-- begin #sidebar -->
    <div id="sidebar" class="sidebar">
        <!-- begin sidebar scrollbar -->
        <div data-scrollbar="true" data-height="100%">
            <!-- begin sidebar user -->
            <ul class="nav">
    					<li class="nav-profile">
    						<div class="image">
    							<a href="javascript:;"><img :src="userinfo.avatar" alt="" /></a>
    						</div>
    						<div class="info">
    							{{userinfo.domain_name}}
    							<small>{{userinfo.name}}</small>
    						</div>
    					</li>
    				</ul>
            <!-- end sidebar user -->
            <!-- begin sidebar nav -->
            <ul class="nav">
                <li class="nav-header">主菜单</li>
                <template v-for="item in userinfo.menulist">
                  <template v-if="item.menu_type === '00'">
                      <li class="has-sub">
                          <a href="javascript:;">
                              <b class="caret pull-right"></b>
                              <i :class="'fa ' + item.menu_icon"></i>
                              <span>{{ item.menu_name }}</span>
                              <div v-if="item.menu_name === '事务管理'  " class="count_remind" style="background: red;border-radius: 10px;text-align: center;position: absolute;top: 8px;right: 34px">
                                  <span style="color: #fff;margin: 7px" class="unfinished_counts"></span>
                              </div>
                          </a>

                          <ul class="sub-menu">
                              <template v-for="sItem in item.sub_menu">
                                <template v-if="sItem.menu_type === '00'">
                                  <li class="has-sub">
                                      <a href="javascript:;">
                                          <b class="caret pull-right"></b>
                                          {{ sItem.menu_name }}
                                      </a>
                                      <ul class="sub-menu">
                                      <template v-for="ssItem in sItem.sub_menu">
                                        <li><router-link :to="ssItem.menu_path"> {{ ssItem.menu_name }}</router-link></li>
                                      </template>
                                      </ul>
                                  </li>
                                </template>
                                <template v-if="sItem.menu_type === '01' && sItem.show_flag === '1'">
                                  <li>
                                      <router-link :to="sItem.menu_path">{{ sItem.menu_name }}
                                      <div v-if="sItem.menu_id === 453" class="count_remind" style="background: red;border-radius: 10px;text-align: center;position: absolute;right: 34px;top: 5px">
                                        <span style="color: #fff;margin: 4px" class="unfinished_counts"></span>
                                      </div>
                                      </router-link>
                                  </li>
                                </template>
                              </template>
                          </ul>
                      </li>
                  </template>
                  <template v-if="item.menu_type === '01'">
                    <li>
                        <router-link :to="item.menu_path">
                          <i v-bind:class="'fa ' + item.menu_icon "></i> <span>{{ item.menu_name }}</span>
                        </router-link>
                    </li>
                  </template>
              </template>
               <!--begin sidebar minify button-->
    					<li><a href="javascript:;" class="sidebar-minify-btn" data-click="sidebar-minify"><i class="fa fa-angle-double-left"></i></a></li>
    					<!-- end sidebar minify button -->
            </ul>
            <!-- end sidebar nav -->
        </div>
        <!-- end sidebar scrollbar -->
    </div>
    <div class="sidebar-bg"></div>
    <!-- end #sidebar -->
</div>
</template>
<script>
const common = require('commonFunc')
export default {
    name: 'mainMenu',
    data: function() {
        return {
            userinfo: common.getStoreData('userinfo'),
            divHeight: '',
        }
    },
    mounted: function() {
        let _self = this;
        let param = {
            domain_id : _self.userinfo.domain_id,
            current_type : 1,
            task_state: '1'
        }
        _self.$http.post('/api/nca/homepage/NCAHomePageControl?method=search_t', param).then((response) => {
            let unfinished_counts = response.data.info.total;
            if(unfinished_counts === 0){
                $(".count_remind").hide()
            }else{
                $(".unfinished_counts").html(unfinished_counts);
            }
//            alert(unfinished_counts);
        },(error) =>{
            common.dealErrorCommon(_self, error)
        })
    },
}
</script>
<style scoped>
</style>
