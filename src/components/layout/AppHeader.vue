<template>
  <v-app-bar flat elevation="2" color="error">
    <v-app-bar-nav-icon @click="handleMenuClick" class="d-md-none" v-if="!isLoginPage"/>
    <v-app-bar-title>
      Transhub：中国人民大学“一人一栈”训练平台
    </v-app-bar-title>
    <template v-if="isLoginPage">
      <a
        href="https://www.litonglab.com/"
        target="_blank"
        rel="noopener noreferrer"
        class="header-logo-link"
      >
        <img
          src="../../assets/litonglab-logo-long.png"
          class="header-logo"
          alt="LitongLab Logo"
        />
      </a>
    </template>
    <template v-else>
      <v-spacer></v-spacer>
      <v-chip
        :color="roleConfig[role]?.color || 'green'"
        size="small"
        variant="elevated"
        class="mr-2"
        v-if="role !== 'student'"
      >
        {{ roleConfig[role]?.text || "学生" }}
      </v-chip>
      <v-chip color="teal" size="small" variant="elevated">
        Version: 3.0
      </v-chip>
      <v-btn class="fill-height">
        {{ userName }}
      </v-btn>
      <v-btn @click="logout" class="fill-height">退出登录</v-btn>
    </template>
  </v-app-bar>
</template>

<script setup>
import {computed} from "vue";
import {useRoute, useRouter} from "vue-router";
import {useAppStore} from "@/store/app.js";
import {APIS} from "@/config";
import {request} from "@/utility";

const store = useAppStore();
const router = useRouter();

const role = computed(() => store.role);
const userName = computed(() => store.name);

const route = useRoute();
const isLoginPage = computed(() => {
  return (
    route.name === "login" ||
    window.location.pathname === "/login" ||
    window.location.pathname === "/"
  );
});

const roleConfig = {
  super_admin: {color: "purple", text: "超级管理员"},
  admin: {color: "primary", text: "管理员"},
  student: {color: "green", text: "学生"},
};

// 移动端菜单按钮直接派发全局事件
function handleMenuClick() {
  const route = router.currentRoute.value;
  if (route.path.startsWith("/transhub")) {
    window.dispatchEvent(new CustomEvent("toggle-home-drawer"));
  }
}

async function logout() {
  try {
    await request(APIS.logout, {method: "GET"});
    store.clear_user_info();
    await router.push({name: "login"});
  } catch (error) {
  }
}
</script>

<style scoped>
.header-logo-link {
  display: flex;
  align-items: center;
  margin-left: 16px;
}

.header-logo {
  height: 40px;
}
</style>
