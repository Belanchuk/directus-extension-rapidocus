<script setup>
import "rapidoc";
import { useApi } from "@directus/extensions-sdk";
import { computed, onMounted, ref, watch } from "vue";

const api = useApi();

const rapidoc = ref(null);
const schema = ref("");
const url = ref("");
var timer;

// General
const display_mode = ref("focused");
const display_modes = ref(["view", "read", "focused"]);
const allow_try = ref(true);

// Header
const show_header = ref(true);
const allow_spec_url_load = ref(false);
const allow_spec_file_load = ref(false);

// Navigation
const allow_search = ref(true);
const allow_advanced_search = ref(true);
const show_info = ref(false);
const allow_server_selection = ref(false);
const allow_authentication = ref(false);
const persist_auth = ref(false);
const font_spacing = ref("default");
const font_spacings = ref(["compact", "default", "relaxed"]);
const font_size = ref("default");
const font_sizes = ref(["default", "large", "largest"]);
const nav_active_item_marker = ref("left-bar");
const nav_active_items_marker = ref(["left-bar", "colored-block"]);
const show_method_in_nav_bar = ref("as-colored-block");
const show_methods_in_nav_bar = ref([
  "false",
  "as-plain-text",
  "as-colored-text",
  "as-colored-block",
]);
//

onMounted(async () => {
  url.value = location.origin + "/server/specs/oas";
  const { data } = await api.get("/server/specs/oas");
  schema.value = data;
});

const loadSpec = async () => {
  rapidoc.value.loadSpec(url.value);
};

watch(schema, (schema) => {
  clearTimeout(timer);
  timer = setTimeout(() => {
    rapidoc.value.loadSpec(schema);
  }, 100);
});

const colorScheme = window.matchMedia("(prefers-color-scheme: dark)");
colorScheme.addEventListener("change", (event) => {
  mode.value = event.matches ? "dark" : "light";
});

const mode = ref(colorScheme.matches ? "dark" : "light");
const colors = computed(() => {
  if (mode.value === "dark") {
    return {
      header: "#0d1117",
      bg: "#161b22",
      text: "#ffffff",
      primary: "#8866ff",
      navBg: "#21262e",
      navText: "#ffffff",
    };
  } else {
    return {
      header: "#ffffff",
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
      v-if="schema"
      ref="rapidoc"
      style="height: calc(100% - 60px); width: 100%"
      :render-style="display_mode"
      :theme="mode"
      :show-header="show_header"
      :show-info="show_info"
      :allow-spec-file-load="allow_spec_file_load"
      :allow-spec-url-load="allow_spec_url_load"
      :allow-server-selection="allow_server_selection"
      :allow-authentication="allow_authentication"
      :allow-search="allow_search"
      :allow-advanced-search="allow_advanced_search"
      :show-method-in-nav-bar="show_method_in_nav_bar"
      :nav-active-item-marker="nav_active_item_marker"
      :nav-item-spacing="font_spacing"
      :font-size="font_size"
      :allow-try="allow_try"
      :header-color="colors.header"
      :bg-color="colors.bg"
      :primary-color="colors.primary"
      :nav-bg-color="colors.navBg"
      :nav-text-color="colors.navText"
      :persist-auth="persist_auth"
      allow-schema-description-expand-toggle="true"
    >
    </rapi-doc>

    <template #actions:prepend>
      <v-input v-model="url" small placeholder="Paste OpenAPI Spec url" />
      <v-button
        class="pl20"
        secondary
        icon
        small
        @click="loadSpec"
        v-tooltip.bottom="'Get OpenAPI Spec'"
      >
        <v-icon name="download" />
      </v-button>
    </template>

    <template #sidebar>
      <sidebar-detail icon="settings" title="General settings" close>
        <div class="layout-options">
          <div class="pb20">
            <div class="type-label">Display mode</div>
            <v-select v-model="display_mode" :items="display_modes"> </v-select>
          </div>
        </div>
        <v-checkbox v-model="allow_try">allow_try</v-checkbox>
      </sidebar-detail>
      <sidebar-detail icon="top_panel_close" title="Header">
        <div class="layout-options">
          <v-checkbox v-model="show_header">Show header</v-checkbox>
          <v-checkbox v-model="allow_spec_url_load"
            >Allow spec url load</v-checkbox
          >
          <v-checkbox v-model="allow_spec_file_load"
            >Allow spec file load</v-checkbox
          >
        </div>
      </sidebar-detail>
      <sidebar-detail icon="left_panel_close" title="Navigation">
        <div class="layout-options">
          <div class="pb20">
            <div class="type-label">Show method in navbar</div>
            <v-select
              v-model="show_method_in_nav_bar"
              :items="show_methods_in_nav_bar"
            >
            </v-select>
          </div>
          <div class="pb20">
            <div class="type-label">Navbar active item</div>
            <v-select
              v-model="nav_active_item_marker"
              :items="nav_active_items_marker"
            >
            </v-select>
          </div>
          <div class="pb20">
            <div class="type-label">Font spacing</div>
            <v-select v-model="font_spacing" :items="font_spacings"> </v-select>
          </div>
          <div class="pb20">
            <div class="type-label">Font size</div>
            <v-select v-model="font_size" :items="font_sizes"> </v-select>
          </div>
          <v-checkbox v-model="show_info">Show info</v-checkbox>
          <v-checkbox v-model="allow_server_selection"
            >Allow server selection</v-checkbox
          >
          <v-checkbox v-model="allow_authentication"
            >Allow authentication</v-checkbox
          >
          <v-checkbox v-model="persist_auth">Persist auth</v-checkbox>
          <v-checkbox v-model="allow_search">Allow search</v-checkbox>
          <v-checkbox v-model="allow_advanced_search"
            >Allow advanced search</v-checkbox
          >
        </div>
      </sidebar-detail>
    </template>
  </private-view>
</template>

<style>
.flex {
  display: flex;
}
.center {
  align-content: center;
}
.h48 {
  height: 48px;
}
.pb20 {
  padding-bottom: 20px;
}
.pl20 {
  padding-left: 20px;
}
.api-rapidocus #navigation .module-nav {
  display: none !important;
}
</style>
