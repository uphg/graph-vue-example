<template>
  <div ref="container" style="width: 100%; height: 100vh"></div>
</template>

<script setup>
import { Graph, treeToGraphData } from '@antv/g6';
import { ref, onMounted } from 'vue';

const container = ref(null);

onMounted(async () => {
  const animation = {
    duration: 500,
    easing: 'linear',
  };
  const toolbarMap = {
    'zoom-in': () => {
      graph.zoomBy(1.2, animation); 
    },
    'zoom-out': () => {
      graph.zoomBy(0.8, animation);
    },
    reset: () => {
      graph.render();
    },
  }

  fetch('https://assets.antv.antgroup.com/g6/element-nodes.json').then((res) => {
    console.log('res')
    console.log(res)
  })

  const data = await fetch('https://gw.alipayobjects.com/os/antvdemo/assets/data/algorithm-category.json').then((res) => res.json())
  const graphData = treeToGraphData(data)

  const graph = new Graph({
    container: container.value,
    autoFit: 'view',
    data: graphData,
    behaviors: [
      'drag-element','drag-canvas', 'zoom-canvas',
      {
        type: 'hover-activate',
        degree: 1, // 👈🏻 Activate relations.
      },
      {
        type: 'click-select',
        // inactiveState: 'dim',
        // degree: true,
        multiple: true,
        trigger: ['shift'],
        // unselectedState: 'inactive',
        unselectedState: 'dim',
      },
    ],

    node: {
      style: {
        size: 60,
        labelPlacement: 'center',
        labelText: (d) => d.id,
        labelWordWrap: true,
        labelMaxWidth: '90%',
        labelFill: 'white',
        opacity: 1,
        lineWidth: 1,
      },
      state: {
        selected: {
          // fill: 'red',
          color: 'red',
          lineWidth: 2,
        },
        dim: {
          opacity: 0.4,
        },
      }
    },
    edge: {
      type: 'polyline',
      style: {
        startArrow: true,
        // endArrow: true,
        // stroke: '#F6BD16',
        // stroke: '#e0e0e0',
        opacity: 1,
      },
      state: {
        // selected: {
        //   stroke: '#fbc353',
        // },
        dim: {
          // stroke: '#cbd5e7',
          opacity: 0.4,
        },
      },
    },
    layout: {
      type: 'dendrogram',
      radial: true,
      nodeSep: 40,
      rankSep: 140,
      // rankSep: 180, // 增加斥力
    },
    plugins: [
      {
        type: 'toolbar',
        position: 'top-left',
        onClick: (item) => {
          console.log('item clicked:' + item);

          toolbarMap[item]?.();
        },
        getItems: () => {
          // G6 内置了 9 个 icon，分别是 zoom-in、zoom-out、redo、undo、edit、delete、auto-fit、export、reset
          return [
            { id: 'zoom-in', value: 'zoom-in' },
            { id: 'zoom-out', value: 'zoom-out' },
            // { id: 'redo', value: 'redo' },
            // { id: 'undo', value: 'undo' },
            // { id: 'edit', value: 'edit' },
            // { id: 'delete', value: 'delete' },
            // { id: 'auto-fit', value: 'auto-fit' },
            // { id: 'export', value: 'export' },
            { id: 'reset', value: 'reset' },
          ];
        },
      },
      {
        type: 'tooltip',
        // trigger: 'click',
        getContent: (e, items) => {
          let result = `<h4>Custom Content</h4>`;
          items.forEach((item) => {
            result += `<p>名称: ${item.id}</p>`;
          }); 
          return result;
        },
      },
    ],
  });

  graph.render();
})
</script>
