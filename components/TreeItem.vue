<template>
  <li @click.stop="select(model)" class="vue-async-tree-item">
    <div class="vue-async-tree-row" :class="{
      'vue-async-tree-selected': isSelected,
    }">
      <slot name="item-toggle" :model="model" :expandable="expandable" :isExpanded="isExpanded" :isLoading="isLoading"
        :expand="expand" :collapse="collapse">
        <span class="vue-async-tree-toggle" v-if="!isLoading">
          <ToggleButton v-if="expandable(model)" :isExpanded="isExpanded" :expand="expand" :collapse="collapse"
            :model="model" />
        </span>

        <span class="vue-async-tree-loading" v-else>
          <Loading v-if="isLoading" />
        </span>
      </slot>
      <slot name="item-icon" :model="model">
      </slot>
      <slot name="item-text" :model="model">
        {{ model[text] }}
      </slot>
    </div>

    <ul v-show="isExpanded" v-if="expandable(model)" style="padding-left: 1rem">
      <TreeItem v-for="(child, index) in model[children] || []" :model="model[children][index]" :key="index">
        <template v-for="(_, name) in $slots" #[name]="slotData">
          <slot :name="name" v-bind="slotData || {}" />
        </template>
      </TreeItem>
    </ul>
  </li>
</template>

<script setup>
import { computed, inject } from "vue";
import ToggleButton from "./utils/ToggleButton.vue";
import Loading from "./utils/Loading.vue";

const props = defineProps({
  model: Object,
});

const options = inject("options");
const { id, text, group, children } = options;
const select = inject("select");
const expand = inject("expand");
const collapse = inject("collapse");
const selectedItem = inject("selectedItem");
const expandedIds = inject("expandedIds");
const loadingIds = inject("loadingIds");
const expandable = inject("expandable");

const isSelected = computed(() => {
  return selectedItem.value ? props.model[id] === selectedItem.value[id] : false;
});

const isLoading = computed(() => {
  return loadingIds.has(props.model[id]);
});

const isExpanded = computed(() => {
  return expandedIds.has(props.model[id]);
});
</script>

<style scoped>
.vue-async-tree-item {
  cursor: pointer;
  line-height: 1.5;
  list-style-type: none;
  margin: 0px;
}

.vue-async-tree-row {
  display: flex;
}

.vue-async-tree-selected {
  color: #1e1f1f;
  background-color: #dde2e4 !important;
}

.vue-async-tree-toggle {
  width: 35px;
}

.vue-async-tree-loading {
  width: 35px;
  height: 35px;
}
</style>
