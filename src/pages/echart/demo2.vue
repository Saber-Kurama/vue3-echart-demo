<script lang="ts" setup>
// 全量引入
import G6 from '@antv/g6'
import threeFetchData from '../../data/three.json'

defineOptions({ name: 'EchartDemo2' })
const g6DomRef = ref()
const graphRef = ref()

const treeData: any = threeFetchData.data
const nodesets = new Set()
const nodeList: any[] = []
function transformNodeData(d: any[], nodes: Set<any>, nodeList: any[], layer: number) {
  d.forEach((_d) => {
    if (nodes.has(_d.cid || _d.pid)) {
      // 先删除
      const _id = (_d.cid || _d.pid)
      const index = nodeList.findIndex((x) => {
        return x.id === _id
      })
      const _layer = nodeList[index].layer
      if (_layer < layer) {
        nodeList.splice(index, 1, {
          id: _d.cid || _d.pid,
          label: _d.name || _d.pname,
          layer,
        })
      }
    }
    else {
      nodes.add(_d.cid || _d.pid)
      nodeList.push({
        id: _d.cid || _d.pid,
        label: _d.name || _d.pname,
        layer,
      })
    }

    if (_d.children?.length > 0)
      transformNodeData(_d.children, nodes, nodeList, layer + 1)
  })
}

transformNodeData([treeData], nodesets, nodeList, 0)

nodeList.sort((a, b) => a.layer - b.layer)

const edgeList: any[] = []

function transformEdgesData(d: any[], edgeList: any[]) {
  d.forEach((_d) => {
    if (_d.cid) {
      edgeList.push({
        source: _d.parentId || _d.pid,
        target: _d.cid,
      })
    }
    else {
      // 代表第一层 什么也不错

    }

    if (_d.children?.length > 0)
      transformEdgesData(_d.children, edgeList)
  })
}
transformEdgesData([treeData], edgeList)

const data = {
  // nodes: [
  //   {
  //     id: '0',
  //     label: '0',
  //   },
  //   {
  //     id: '1',
  //     label: '1',
  //   },
  //   {
  //     id: '2',
  //     label: '2',
  //   },
  //   {
  //     id: '3',
  //     label: '3',
  //   },
  //   {
  //     id: '4',
  //     label: '4',
  //   },
  //   {
  //     id: '5',
  //     label: '5',
  //   },
  //   {
  //     id: '6',
  //     label: '6',
  //   },
  //   {
  //     id: '7',
  //     label: '7',
  //   },
  //   {
  //     id: '8',
  //     label: '8',
  //   },
  //   {
  //     id: '9',
  //     label: '9',
  //   },
  //   {
  //     id: '10',
  //     label: '10',
  //   },
  //   {
  //     id: '11',
  //     label: '11',
  //   },
  //   {
  //     id: '12',
  //     label: '12',
  //   },
  //   {
  //     id: '13',
  //     label: '13',
  //   },
  //   {
  //     id: '14',
  //     label: '14',
  //   },
  //   {
  //     id: '15',
  //     label: '15',
  //   },
  // ],
  nodes: nodeList,
  edges: edgeList,
  // edges: [
  //   {
  //     source: '0',
  //     target: '1',
  //   },
  //   {
  //     source: '0',
  //     target: '2',
  //   },
  //   {
  //     source: '0',
  //     target: '3',
  //   },
  //   {
  //     source: '0',
  //     target: '4',
  //   },
  //   {
  //     source: '0',
  //     target: '5',
  //   },
  //   {
  //     source: '0',
  //     target: '7',
  //   },
  //   {
  //     source: '0',
  //     target: '8',
  //   },
  //   {
  //     source: '0',
  //     target: '9',
  //   },
  //   {
  //     source: '0',
  //     target: '10',
  //   },
  //   {
  //     source: '0',
  //     target: '11',
  //   },
  //   {
  //     source: '0',
  //     target: '13',
  //   },
  //   {
  //     source: '0',
  //     target: '14',
  //   },
  //   {
  //     source: '0',
  //     target: '15',
  //   },
  //   {
  //     source: '2',
  //     target: '3',
  //   },
  //   {
  //     source: '4',
  //     target: '5',
  //   },
  //   {
  //     source: '4',
  //     target: '6',
  //   },
  //   {
  //     source: '5',
  //     target: '6',
  //   },
  //   {
  //     source: '7',
  //     target: '13',
  //   },
  //   {
  //     source: '8',
  //     target: '14',
  //   },
  //   {
  //     source: '9',
  //     target: '10',
  //   },
  //   {
  //     source: '10',
  //     target: '14',
  //   },
  //   {
  //     source: '10',
  //     target: '12',
  //   },
  //   {
  //     source: '11',
  //     target: '14',
  //   },
  //   {
  //     source: '12',
  //     target: '13',
  //   },
  // ],
}

onMounted(() => {
  if (g6DomRef.value) {
    const width = g6DomRef.value.scrollWidth
    const height = g6DomRef.value.scrollHeight || 800
    // graphRef.value = new G6.Graph({
    //   container: g6DomRef.value,
    // });
    graphRef.value = new G6.Graph({
      container: g6DomRef.value,
      width,
      height,
      fitView: true,
      modes: {
        default: ['drag-canvas', 'zoom-canvas', 'activate-relations'],
      },
      layout: {
        type: 'dagre',
        rankdir: 'LR',
        align: 'DL',
        // align: "UL",
        nodesep: 0,
        // nodesepFunc: () => 2,
        ranksepFunc: () => 200,
        controlPoints: true,
      },
      defaultNode: {
        size: 12,
        // type: 'rect',
        style: {
          lineWidth: 2,
          stroke: '#5B8FF9',
          fill: '#C6E5FF',
        },
        labelCfg: {
          position: 'right',
          // 节点上的标签文本样式配置
          style: {
            fontFamily: 'sans-serif',
            // fill: '#666', // 节点标签文字颜色
            fontSize: 12,
          },
        },
      },
      defaultEdge: {
        size: 1,
        color: '#e2e2e2',
        style: {
          endArrow: {
            path: 'M 0,0 L 8,4 L 8,-4 Z',
            fill: '#e2e2e2',
          },
        },
      },
    })
    graphRef.value.data(data)
    graphRef.value.render()
  }
})
</script>

<template>
  <div>
    <div ref="g6DomRef" style="height: 900px" />
  </div>
</template>
