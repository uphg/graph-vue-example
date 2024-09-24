<template>
  <div ref="container" style="width: 100%; height: 100vh"></div>
</template>

<script setup>
import { Graph, treeToGraphData } from '@antv/g6';
import { ref, onMounted } from 'vue';

const container = ref(null);

onMounted(async () => {

  fetch('https://assets.antv.antgroup.com/g6/element-nodes.json')
  .then((res) => {
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
        type: 'click-select',
        // inactiveState: 'dim',
        degree: true,
        multiple: true,
        trigger: ['shift'],
        unselectedState: 'inactive',
        // unselectedState: 'dim',
      },
      // {
      //   type: 'hover-activate',
      //   degree: 1, // 👈🏻 Activate relations.
      // },
    ],

    node: {
      style: {
        size: 60,
        labelPlacement: 'center',
        labelText: (d) => d.id,
        labelWordWrap: true,
        labelMaxWidth: '90%',
        labelFill: 'white',
      },
      // animation: {
      //   enter: false,
      // },
      state: {
        dim: {
          fill: '#cbd5e7',
          // lineColor: '#cbd5e7',
          // stroke: '#cbd5e7',
        },
      },
    },
    edge: {
      type: 'polyline',
      style: {
        startArrow: true,
        // endArrow: true,
        // stroke: '#F6BD16',
        stroke: '#000',
      },
      state: {
        dim: {
          stroke: '#cbd5e7',
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
        type: 'tooltip',
        trigger: 'click',
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
