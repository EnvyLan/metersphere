<template>
  <div id="menu-bar">
    <el-row type="flex">
      <el-col :span="10">
        <el-menu class="header-menu" :unique-opened="true" mode="horizontal" router :default-active='$route.path'>

          <el-submenu v-permission="['test_manager','test_user','test_viewer']"
                      index="3" popper-class="submenu">
            <template v-slot:title>
              <span style="display: inline-block;width: 150px;white-space:nowrap; overflow:hidden; text-overflow:ellipsis;" :title="currentProject">
                {{ $t('commons.project') }}: {{currentProject}}
              </span>
            </template>
            <search-list ref="projectRecent" :options="projectRecent" :current-project.sync="currentProject"/>
            <el-divider/>
            <el-menu-item :index="'/setting/project/create'">
              <font-awesome-icon :icon="['fa', 'plus']"/>
              <span style="padding-left: 7px;">{{ $t("project.create") }}</span>
            </el-menu-item>
            <ms-show-all :index="'/setting/project/all'"/>
          </el-submenu>

          <el-menu-item :index="'/performance/home'">
            {{ $t("i18n.home") }}
          </el-menu-item>

          <el-submenu v-permission="['test_manager','test_user','test_viewer']"
                      index="4" popper-class="submenu">
            <template v-slot:title>{{ $t('commons.test') }}</template>
            <ms-recent-list ref="testRecent" :options="testRecent"/>
            <el-divider/>
            <ms-show-all :index="'/performance/test/all'"/>
            <ms-create-button v-permission="['test_manager','test_user']" :index="'/performance/test/create'"
                              :title="$t('load_test.create')"/>
          </el-submenu>

          <el-submenu v-permission="['test_manager','test_user','test_viewer']"
                      index="5" popper-class="submenu">
            <template v-slot:title>{{ $t('commons.report') }}</template>
            <ms-recent-list ref="reportRecent" :options="reportRecent"/>
            <el-divider/>
            <ms-show-all :index="'/performance/report/all'"/>
          </el-submenu>
        </el-menu>
      </el-col>
      <el-col :span="4" >
        <el-row type="flex" justify="center">
          <ms-create-test :to="'/performance/test/create'"/>
        </el-row>
      </el-col>
      <el-col :span="10"/>
    </el-row>
  </div>
</template>

<script>

import MsCreateTest from "../../common/head/CreateTest";
import MsRecentList from "../../common/head/RecentList";
import MsCreateButton from "../../common/head/CreateButton";
import MsShowAll from "../../common/head/ShowAll";
import {LIST_CHANGE, PerformanceEvent} from "@/business/components/common/head/ListEvent";
import SearchList from "@/business/components/common/head/SearchList";

export default {
  name: "PerformanceHeaderMenus",
  components: {
    SearchList,
    MsCreateButton,
    MsShowAll,
    MsRecentList,
    MsCreateTest
  },
  data() {
    return {
      projectRecent: {
        title: this.$t('project.recent'),
        url: "/project/recent/5",
        index(item) {
          return '/performance/test/' + item.id;
        },
        router(item) {
          return {name: 'perPlan', params: {projectId: item.id, projectName: item.name}}
        }
      },
      testRecent: {
        title: this.$t('load_test.recent'),
        url: "/performance/recent/5",
        index(item) {
          return '/performance/test/edit/' + item.id;
        },
        router(item) {
        }
      },
      reportRecent: {
        title: this.$t('report.recent'),
        url: "/performance/report/recent/5",
        showTime: true,
        index(item) {
          return '/performance/report/view/' + item.id;
        },
        router(item) {
        }
      },
      currentProject: ''
    }
  },
  methods: {
    registerEvents() {
      PerformanceEvent.$on(LIST_CHANGE, () => {
        // todo 这里偶尔会有 refs 为空的情况
        if (!this.$refs.projectRecent) {
          return;
        }
        this.$refs.projectRecent.recent();
        this.$refs.testRecent.recent();
        this.$refs.reportRecent.recent();
      });
    }
  },
  mounted() {
    this.registerEvents();
  }
}

</script>

<style scoped>
.el-divider--horizontal {
  margin: 0;
}

.el-menu.el-menu--horizontal {
  border-bottom: none;
}

#menu-bar {
  border-bottom: 1px solid #E6E6E6;
  background-color: #FFF;
}
</style>
