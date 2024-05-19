<script setup>
import "rapidoc";
import useLocalStorage from "./useLocalStorage";
import { computed, onMounted, ref, watch } from "vue";

const rapidoc = ref(null);
const key = "rapidocus_api_urls";
const url = ref(`${location.origin}/server/specs/oas`);
const urls = useLocalStorage("rapidocus_api_urls", url.value);
var timer;

// General
const display_mode = useLocalStorage("rapidocus_display_mode", "focused");
const display_modes = ref(["view", "read", "focused"]);
const allow_try = useLocalStorage("rapidocus_allow_try", true);
//

// Navigation
const allow_search = useLocalStorage("rapidocus_allow_search", true);
const allow_advanced_search = useLocalStorage(
  "rapidocus_allow_advanced_search",
  true
);
const show_info = useLocalStorage("rapidocus_show_info", false);
const allow_server_selection = useLocalStorage(
  "rapidocus_allow_server_selection",
  false
);
const allow_authentication = useLocalStorage(
  "rapidocus_allow_authentication",
  false
);
const persist_auth = useLocalStorage("rapidocus_persist_auth", false);
const item_spacing = useLocalStorage("rapidocus_item_spacing", "default");
const item_spacings = ref(["compact", "default", "relaxed"]);
const font_size = useLocalStorage("rapidocus_font_size", "default");
const font_sizes = ref(["default", "large", "largest"]);
const nav_active_item_marker = useLocalStorage(
  "rapidocus_nav_active_item_marker",
  "left-bar"
);
const nav_active_items_marker = ref(["left-bar", "colored-block"]);
const sort_endpoints = useLocalStorage("rapidocus_sort_endpoints", "none");
const sort_endpoints_by = ref(["path", "method", "summary", "none"]);
const show_method_in_nav_bar = useLocalStorage(
  "rapidocus_show_method_in_nav_bar",
  "as-colored-block"
);
const show_methods_in_nav_bar = ref([
  "false",
  "as-plain-text",
  "as-colored-text",
  "as-colored-block",
]);
//

onMounted(async () => {
  clearTimeout(timer);
  timer = setTimeout(() => {
    rapidoc.value.loadSpec(url.value);
  }, 50);
  var value = localStorage.getItem(key);
  if (!value) {
    localStorage.setItem(key, JSON.stringify([url.value]));
  }
});

const loadSpec = async () => {
  await rapidoc.value.loadSpec(url.value);
  var old_value = localStorage.getItem(key);
  const new_value = JSON.parse(old_value);
  if (!new_value.includes(url.value)) {
    new_value.push(url.value);
    localStorage.setItem(key, JSON.stringify(new_value));
  }
};

const setUrl = (args) => {
  url.value = args.target.textContent;
};

const removeUrl = (args) => {
  const value = JSON.parse(localStorage.getItem(key));
  const new_value = value.filter(
    (url) =>
      url !==
      args.target.parentElement.parentElement.previousSibling.textContent
  );
  urls.value = JSON.stringify(new_value);
  localStorage.setItem(key, JSON.stringify(new_value));
};

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
      ref="rapidoc"
      style="height: calc(100% - 60px); width: 100%"
      :render-style="display_mode"
      :theme="mode"
      show-header="false"
      allow-spec-file-load="false"
      allow-spec-url-load="false"
      :show-info="show_info"
      :allow-server-selection="allow_server_selection"
      :allow-authentication="allow_authentication"
      :allow-search="allow_search"
      :allow-advanced-search="allow_advanced_search"
      :show-method-in-nav-bar="show_method_in_nav_bar"
      :nav-active-item-marker="nav_active_item_marker"
      :nav-item-spacing="item_spacing"
      :font-size="font_size"
      :allow-try="allow_try"
      :header-color="colors.header"
      :bg-color="colors.bg"
      :primary-color="colors.primary"
      :nav-bg-color="colors.navBg"
      :nav-text-color="colors.navText"
      :persist-auth="persist_auth"
      :sort-endpoints-by="sort_endpoints"
      allow-schema-description-expand-toggle="false"
    >
    </rapi-doc>

    <template #actions:prepend>
      <v-menu placement="bottom-end" show-arrow toggle>
        <template #activator="{ toggle }">
          <v-input
            @click="toggle"
            v-model="url"
            placeholder="Paste OpenAPI Spec url in json or yaml format."
            small
          />
        </template>
        <v-list>
          <template v-for="url in urls" :key="index">
            <v-list-item clickable @click="setUrl">
              <v-list-item-content>{{ url }}</v-list-item-content>
              <v-list-item-icon @click.stop="removeUrl">
                <v-icon name="delete" />
              </v-list-item-icon>
            </v-list-item>
          </template>
        </v-list>
      </v-menu>

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
        <div class="pb20">
          <div class="flex">
            <div class="type-label">Display mode</div>
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `Determines display of api-docs.
                  - view: friendly for quick exploring (expand/collapse the section of your interest)
                  - read: suitable for reading (like a continuous web-page)
                  - focused: similar to read but focuses on a single endpoint at a time (good for large specs)`
              "
            ></v-icon>
          </div>
          <v-select v-model="display_mode" :items="display_modes"></v-select>
        </div>
        <div class="flex center">
          <v-checkbox class="type-label" v-model="allow_try"
            >Allow try
          </v-checkbox>
          <v-icon
            class="small pl5"
            name="help"
            v-tooltip.left="
              `The 'TRY' feature allows you to make REST calls to the API server. To disable this feature, set it to false.`
            "
          ></v-icon>
        </div>
      </sidebar-detail>
      <sidebar-detail icon="left_panel_close" title="Navigation">
        <div class="layout-options">
          <div class="pb20">
            <div class="flex">
              <div class="type-label">Show method in navbar</div>
              <v-icon
                class="small pl5"
                name="help"
                v-tooltip.left="`Shows API Method names in the navigation bar.`"
              ></v-icon>
            </div>
            <v-select
              v-model="show_method_in_nav_bar"
              :items="show_methods_in_nav_bar"
            >
            </v-select>
          </div>
          <div class="pb20">
            <div class="flex">
              <div class="type-label">Sort endpoints</div>
              <v-icon
                class="small pl5"
                name="help"
                v-tooltip.left="
                  `Sort endpoints within each tag by path, method or summary. none leaves the sort order unmodified. Applies after page reload.`
                "
              ></v-icon>
            </div>
            <v-select v-model="sort_endpoints" :items="sort_endpoints_by">
            </v-select>
          </div>
          <div class="pb20">
            <div class="flex">
              <div class="type-label">Navbar active item</div>
              <v-icon
                class="small pl5"
                name="help"
                v-tooltip.left="`Navigation active item indicator styles.`"
              ></v-icon>
            </div>
            <v-select
              v-model="nav_active_item_marker"
              :items="nav_active_items_marker"
            >
            </v-select>
          </div>
          <div class="pb20">
            <div class="flex">
              <div class="type-label">Item spacing</div>
              <v-icon
                class="small pl5"
                name="help"
                v-tooltip.left="`Controls navigation item spacing.`"
              ></v-icon>
            </div>
            <v-select v-model="item_spacing" :items="item_spacings"> </v-select>
          </div>
          <div class="pb20">
            <div class="flex">
              <div class="type-label">Font size</div>
              <v-icon
                class="small pl5"
                name="help"
                v-tooltip.left="
                  `Sets the relative font sizes for the entire document.`
                "
              ></v-icon>
            </div>
            <v-select v-model="font_size" :items="font_sizes"> </v-select>
          </div>
          <div class="flex center">
            <v-checkbox v-model="show_info">Show info</v-checkbox>
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `show/hide the documents info section. Info section contains information about the spec, such as the title and description of the spec, the version, terms of services etc. In certain situation you may not need to show this section. For instance you are embedding this element inside a another help document. Chances are, the help doc may already have this info, in that case you may want to hide this section.`
              "
            ></v-icon>
          </div>

          <div class="flex center">
            <v-checkbox v-model="allow_server_selection"
              >Allow server selection</v-checkbox
            >
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `If set to 'false', user will not be able to see or select API server (Server List will be hidden, however users will be able to see the server url near the 'TRY' button, to know in advance where the TRY will send the request). The first server in the API specification file will be used.`
              "
            ></v-icon>
          </div>

          <div class="flex center">
            <v-checkbox v-model="allow_authentication"
              >Allow authentication</v-checkbox
            >
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `Authentication feature, allows the user to select one of the authentication mechanism thats available in the spec. It can be http-basic, http-bearer or api-key.`
              "
            ></v-icon>
          </div>

          <div class="flex center">
            <v-checkbox v-model="persist_auth">Persist auth</v-checkbox>
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `Authentication will be persisted to localStorage.`
              "
            ></v-icon>
          </div>

          <div class="flex center">
            <v-checkbox v-model="allow_search">Allow search</v-checkbox>
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="`Provides quick filtering of API.`"
            ></v-icon>
          </div>

          <div class="flex center">
            <v-checkbox v-model="allow_advanced_search"
              >Allow advanced search</v-checkbox
            >
            <v-icon
              class="small pl5"
              name="help"
              v-tooltip.left="
                `Provides advanced search functionality, to search through API-paths, API-description, API-parameters and API-Responses.`
              "
            ></v-icon>
          </div>
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
  align-items: center;
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
.pl5 {
  padding-left: 5px;
}
.small {
  height: 10px;
}
.api-rapidocus #navigation .module-nav {
  display: none !important;
}
</style>
