<script setup>
import "rapidoc";
import { useApi } from "@directus/extensions-sdk";
import { computed, onMounted, ref, watch } from "vue";

const api = useApi();

const rapidoc = ref(null);
const rapidocus_schema = ref("");

const show_header = ref(false);
const show_info = ref(false);
const allow_server_selection = ref(false);

const nav_active_item_marker = ref("colored-block");
const nav_active_items_marker = ref(["left-bar", "colored-block"]);

const show_method_in_nav_bar = ref("as-colored-block");
const show_methods_in_nav_bar = ref([
  "false",
  "as-plain-text",
  "as-colored-text",
  "as-colored-block",
]);

const display_mode = ref("read".toLowerCase());
const display_modes = ref(["view", "read", "focused"]);

const persist_auth = ref(false);

var timer;

onMounted(async () => {
  const { data } = await api.get("/server/specs/oas");
  rapidocus_schema.value = data;
});

watch(rapidocus_schema, (schema) => {
  clearTimeout(timer);
  timer = setTimeout(() => {
    schema.servers[0].url = location.origin;
    rapidoc.value.loadSpec(schema);
  }, 100);
});

const colorScheme = window.matchMedia("(prefers-color-scheme: dark)");
const mode = ref(colorScheme.matches ? "dark" : "light");

colorScheme.addEventListener("change", (event) => {
  mode.value = event.matches ? "dark" : "light";
});

const colors = computed(() => {
  if (mode.value === "dark") {
    return {
      bg: "#161b22",
      text: "#ffffff",
      primary: "#8866ff",
      navBg: "#21262e",
      navText: "#ffffff",
    };
  } else {
    return {
      bg: "#ffffff",
      text: "#21262e",
      primary: "#8866ff",
      navBg: "#f0f4f9",
      navText: "#161b22",
    };
  }
});
</script>

<template>
  <private-view title="Rapidocus" small-header class="api-rapidocus">
    <rapi-doc
      v-if="rapidocus_schema"
      ref="rapidoc"
      style="height: 100%; width: 100%"
      :show-method-in-nav-bar="show_method_in_nav_bar"
      :nav-active-item-marker="nav_active_item_marker"
      :show-header="show_header"
      :show-info="show_info"
      :allow-server-selection="allow_server_selection"
      allow-authentication="false"
      allow-search="true"
      allow-advanced-search="true"
      allow-try="true"
      :render-style="display_mode"
      :theme="mode"
      :bg-color="colors.bg"
      :primary-color="colors.primary"
      :nav-bg-color="colors.navBg"
      :nav-text-color="colors.navText"
      :persist-auth="persist_auth"
    ></rapi-doc>

    <template #sidebar>
      <sidebar-detail icon="settings" title="General settings" close>
        <div class="layout-options">
          <div class="type-label">Display mode</div>
          <v-select v-model="display_mode" :items="display_modes"> </v-select>
        </div>
      </sidebar-detail>
      <sidebar-detail icon="layers" title="Navigation bar">
        <div class="layout-options">
          <div class="pb20">
            <div class="type-label">Show method in navbar</div>
            <v-select
              v-model="show_method_in_nav_bar"
              :items="show_methods_in_nav_bar"
            >
            </v-select>
          </div>
          <div class="type-label">Navbar active item</div>
          <v-select
            v-model="nav_active_item_marker"
            :items="nav_active_items_marker"
          >
          </v-select>
        </div>
      </sidebar-detail>
    </template>
  </private-view>
</template>

<style>
.pb20 {
  padding-bottom: 20px;
}
.api-rapidocus #navigation .module-nav {
  display: none !important;
}
</style>
