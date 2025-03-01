<template>
  <v-main class="fill-height vertical-container">
    <v-navigation-drawer
        dark
        fixed
        app
        persistent
        class="background"
        mobile-breakpoint="sm"
        width="72"
    >
      <v-layout column fill-height>
        <v-list subheader class="align-center">
          <v-list-item to="/">
            <v-list-item-content>
              <div class="align-center text-center">
                <img src="@/assets/logo_v2_white.png" alt="Giskard icon" width="45px"/>
              </div>
            </v-list-item-content>
          </v-list-item>
          <v-divider/>
          <v-list-item to="/main/projects">
            <v-list-item-content>
              <v-icon>web</v-icon>
              <div class="caption">Projects</div>
            </v-list-item-content>
          </v-list-item>
        </v-list>
        <v-spacer></v-spacer>
        <v-list>
          <v-divider/>
          <v-list-item v-show="hasAdminAccess" to="/main/admin/">
            <v-list-item-content>
              <v-icon>mdi-cog</v-icon>
              <div class="caption">Settings</div>
            </v-list-item-content>
          </v-list-item>
          <v-divider/>
          <v-list-item to="/main/profile/view" v-if="authAvailable">
            <v-list-item-content>
              <v-icon>person</v-icon>
              <div class="caption">{{ userId }}</div>
            </v-list-item-content>
          </v-list-item>
          <v-list-item @click="logout" v-if="authAvailable">
            <v-list-item-content>
              <v-icon>logout</v-icon>
              <div class="caption">Logout</div>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-layout>
    </v-navigation-drawer>

    <div class="pa-1 vertical-container">
      <router-view></router-view>
    </div>
  </v-main>
</template>

<script lang="ts">
import {Component, Vue} from "vue-property-decorator";

import {appName} from "@/env";
import {useUserStore} from "@/stores/user";
import {useMainStore} from "@/stores/main";

const routeGuardMain = async (to, _from, next) => {
  if (to.path === "/main") {
    next("/main/dashboard");
  } else {
    next();
  }
};

@Component
export default class Main extends Vue {
  public appName = appName;
  private miniDrawer = false;

  public beforeRouteEnter(to, from, next) {
    routeGuardMain(to, from, next);
  }

  public beforeRouteUpdate(to, from, next) {
    routeGuardMain(to, from, next);
  }

  public get hasAdminAccess() {
    const userStore = useUserStore();
    return userStore.hasAdminAccess;
  }

  public get authAvailable() {
    const mainStore = useMainStore();
    return mainStore.authAvailable;
  }

  get userId() {
    const userProfile = useUserStore().userProfile;
    if (userProfile) {
      return userProfile.user_id;
    } else {
      return "Guest";
    }
  }

  public async logout() {
    await useUserStore().userLogout();
  }
}
</script>

<style scoped>


.background {
  background-image: url("~@/assets/wallpaper-skyline-reduced.jpg");
  background-position: 0 20%;
  background-size: auto 100%;
}

div.caption {
  font-size: 11px !important;
  align-self: center;
  text-align: center;
}

.v-list-item {
  padding: 0 10px;
}
</style>
<style>
header.v-toolbar a {
  text-decoration: none;
}
</style>
