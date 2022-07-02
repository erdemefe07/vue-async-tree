# vue-async-tree

[![NPM version](https://img.shields.io/npm/v/vue-async-tree.svg?style=flat-square)](https://www.npmjs.com/package/vue-async-tree)

Async featured Tree Component for Vue, **Only tested on Vue 3.**

## Install

```bash
# with npm
npm install vue-async-tree

# or with Yarn
yarn add vue-async-tree
```

## Simple Example

```html
<template>
  <Tree
    :initialData="data"
    :options="options"
    :expandable="expandable"
    :fetchData="fetchData"
  />
</template>

<script setup>
  import Tree from "vue-async-tree";
  const sleep = (ms) => new Promise((r) => setTimeout(r, ms)); // for fetch

  function expandable(item) {
    return item.type === "folder";
  }

  async function fetchData(item) {
    // item is Proxy so can edit as you want
    //  {
    //     _id: "62bd57977de51fb403142fbb",
    //     name: "src",
    //     type: "folder",
    //     items: []
    //   }

    await sleep(300); // fetching

    item[options.children].push({
      _id: "62bf4c269502ccd49487ac6c",
      name: "index.js",
      type: "file",
    });
  }

  const data = [
    {
      _id: "62bd57977de51fb403142fbb",
      name: "src",
      type: "folder",
      items: [],
    },
  ];

  const options = {
    id: "_id",
    text: "name",
    group: "type",
    children: "items",
  };
</script>
```

## Slots

Model is item

| slot        | props                                                      |
| :---------- | ---------------------------------------------------------- |
| item-toggle | model, expandable, isExpanded, isLoading, expand, collapse |
| item-icon   | model                                                      |
| item-text   | model                                                      |

## Example

```html
<template>
  <Tree
    :initialData="data"
    :options="options"
    :expandable="expandable"
    :fetchData="fetchData"
  >
    <template
      #item-toggle="{ model, expandable, isExpanded, isLoading, expand, collapse }"
    >
      <span v-if="isLoading">Loading</span>

      <span v-else-if="expandable(model)">
        <!-- .stop modified required -->
        <button v-if="isExpanded" @click.stop="collapse(model)">
          collapse
        </button>
        <button v-else @click.stop="expand(model)">expand</button>
      </span>
    </template>

    <template #item-icon="{ model }">
      <p v-if="model.type === 'folder'">folderIcon</p>
    </template>

    <template #item-text="{ model }"> - {{ model.name }} - </template>
  </Tree>
</template>
```

## Exposes

| key          | value             |
| :----------- | ----------------- |
| select       | function(item)    |
| expand       | function(item)    |
| collapse     | function(item)    |
| selectedItem | item              |
| loadingIds   | Set (id of items) |

## Styles

Theese are deafults. If you want to change you must use `!important`.

```css
.vue-async-tree-wrapper {
  padding: 0px;
}

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

@-webkit-keyframes fa-spin {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@keyframes fa-spin {
  0% {
    -webkit-transform: rotate(0deg);
    transform: rotate(0deg);
  }

  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

.vue-async-tree-spin {
  -webkit-animation-name: fa-spin;
  animation-name: fa-spin;
  -webkit-animation-delay: var(--fa-animation-delay, 0);
  animation-delay: var(--fa-animation-delay, 0);
  -webkit-animation-direction: var(--fa-animation-direction, normal);
  animation-direction: var(--fa-animation-direction, normal);
  -webkit-animation-duration: var(--fa-animation-duration, 2s);
  animation-duration: var(--fa-animation-duration, 2s);
  -webkit-animation-iteration-count: var(
    --fa-animation-iteration-count,
    infinite
  );
  animation-iteration-count: var(--fa-animation-iteration-count, infinite);
  -webkit-animation-timing-function: var(--fa-animation-timing, linear);
  animation-timing-function: var(--fa-animation-timing, linear);
}

.vue-async-tree-spinner {
  width: 25px;
}

.vue-async-tree-collapse-icon {
  width: 25px;
  height: 25px;
}

.vue-async-tree-expand-icon {
  width: 25px;
  height: 25px;
}
```
