<template>
  <ul class="vue-async-tree-wrapper">
    <TreeItem v-for="(item, index) in data" :key="index" :model="item">
      <template v-for="(_, name) in $slots" #[name]="slotData">
        <slot :name="name" v-bind="slotData || {}" />
      </template>
    </TreeItem>
  </ul>
</template>

<script setup>
import TreeItem from "./TreeItem.vue";

import { ref, reactive, provide } from "vue";
const props = defineProps({
  fetchData: Function,
  options: Object,
  initialData: Array,
  expandable: {
    type: Function,
    default: () => true
  },
});

const { id, children } = props.options;
const data = ref(props.initialData);
const selectedItem = ref();
const expandedIds = reactive(new Set());
const loadingIds = reactive(new Set());

async function getChilds(item) {
  const isArray = Array.isArray(item[children]);
  if (!isArray) item[children] = [];
  else if (item[children].length > 0)
    return;

  loadingIds.add(item[id]);
  await props.fetchData(item);
  loadingIds.delete(item[id]);
}

async function expand(item) {
  await getChilds(item);
  expandedIds.add(item[id]);
}

async function collapse(item) {
  expandedIds.delete(item[id]);
}

function select(item) {
  selectedItem.value = item;
  expand(item);
}

provide("select", select);
provide("expand", expand);
provide("collapse", collapse);
provide("selectedItem", selectedItem);
provide("expandedIds", expandedIds);
provide("loadingIds", loadingIds);
provide("expandable", props.expandable);
provide("options", props.options);

defineExpose({
  select,
  expand,
  collapse,
  selectedItem,
  loadingIds,
});
</script>

<style>
.vue-async-tree-wrapper {
  padding: 0px;
}
</style>