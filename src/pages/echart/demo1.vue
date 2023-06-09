<script lang="ts" setup>
// 全量引入
import * as echarts from 'echarts'
import threeFetchData from '../../data/three.json'

defineOptions({ name: 'EchartDemo1' })
const echartDomRef = ref()
const echartRef = ref()
type EChartsOption = echarts.EChartsOption

// flareData.children.forEach((
//   datum: any,
//   index: number,
// ) => {
//   index % 2 === 0 && (datum.collapsed = true)
// })
const treeData: any = threeFetchData.data
treeData.name = treeData.pname
// 处理 数据
// const transformChildData = (data: any[], treedata: any) => {

// }
const option: EChartsOption = {
  tooltip: {
    trigger: 'item',
    triggerOn: 'mousemove',
  },
  series: [
    {
      type: 'tree',
      zoom: 1,
      data: [treeData as any],
      roam: true,
      top: '1%',
      left: '7%',
      bottom: '1%',
      right: '20%',
      initialTreeDepth: -1,
      // symbolSize: 7,

      label: {
        position: 'left',
        verticalAlign: 'middle',
        align: 'right',
        fontSize: 9,
      },

      leaves: {
        label: {
          position: 'right',
          verticalAlign: 'middle',
          align: 'left',
        },
      },

      emphasis: {
        focus: 'ancestor',
        lineStyle: {
          color: '#ff4500',
          curveness: 0.5,
          width: 2,
          opacity: 0.8,
        },
        blurScope: 'series',
      },

      // expandAndCollapse: true,
      // animationDuration: 550,
      // animationDurationUpdate: 750,
    },
  ],
}

function getDataIndex(data: any[], name: string, dataIndex: number[], index: number) {
  let hasChildName = false
  data.forEach((d) => {
    // let hasChildName = false;
    const _dIdnex = index
    index++
    if (d.children?.length > 0) {
      if (d.name === 'd-data') {
        //  debugger;
      }
      const res = getDataIndex(d.children, name, dataIndex, index)
      index = res.index
      if (res.hasChildName)
        hasChildName = res.hasChildName

      if (d.name === 'd-data') {
        // console.log('d--', d, hasChildName)
      }

      if (res.hasChildName || d.name === name) {
        dataIndex.push(_dIdnex)
        hasChildName = true
      }
    }
    else {
      if (d.name === name) {
        dataIndex.push(_dIdnex)
        hasChildName = true
      }
    }
  })
  return {
    index,
    hasChildName,
  }
}

onMounted(() => {
  if (echartDomRef.value) {
    echartRef.value = echarts.init(echartDomRef.value)
    // echartRef.value.setOption({
    //   title: {
    //     text: 'ECharts 入门示例'
    //   },
    //   tooltip: {},
    //   xAxis: {
    //     data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
    //   },
    //   yAxis: {},
    //   series: [
    //     {
    //       name: '销量',
    //       type: 'bar',
    //       data: [5, 20, 36, 10, 10, 20]
    //     }
    //   ]
    // });

    echartRef.value.setOption(
      option,
    )

    // 监听处理
    echartRef.value.on('highlight', (params: any) => {
      // console.log('highlight', params)
    })
    echartRef.value.on('mouseout', (params: any) => {
      // console.log('downplay', params)
      // // 取消所有强调效果
      echartRef.value.dispatchAction({
        type: 'downplay',
        seriesIndex: 0,

      })
    })
    echartRef.value.on('mouseover', (params: any) => {
      // // 取消所有强调效果
      echartRef.value.dispatchAction({
        type: 'downplay',
        seriesIndex: 0,
        // dataIndex: [1, 2, 3, 4, 6, 15, 14]
      })
      // echartRef.value.dispatchAction({
      //   type: 'downplay',
      //   seriesIndex: 0,
      //   dataIndex: [1, 2, 3, 4, 6, 15, 14]
      // });
      // console.log(echartRef.value.getOption().series[0]);
      //   echartRef.value.getOption().series[0].data.tree.eachNode(function (node: any) {
      //   // if (node.name === 'test') {
      //   //   dataIndex = node.dataIndex;
      //   //   return false;
      //   // }
      //   console.log('sss', node)
      // });
      const dataIndex: any = []
      const index = 1
      const _data = echartRef.value.getOption().series[0].data
      getDataIndex(_data, params.name, dataIndex, index)
      dataIndex.sort((a: number, b: number) => a - b)
      // echartRef.value.dispatchAction({
      //   type: 'highlight',
      //   seriesIndex: 0,
      //   dataIndex,
      //   // dataIndex
      //   // name: [params.name, "CommunityStructure"]
      // });
      // console.log('getDataIndex, dataIndex', dataIndex, _data)
      // //  echartRef.value.dispatchAction({
      // //   type: 'select',
      // //    seriesIndex: 0,
      // //   // dataIndex: [4, 6],
      // //   dataIndex,
      // //   // name: [params.name, "CommunityStructure"]
      // //  });
      // echartRef.value.dispatchAction({
      //   type: 'highlight',
      //   seriesIndex: 0,
      //   // dataIndex: [1, 2, 3, 4, 6, 15, 14],
      //   dataIndex
      //   // name: [params.name, "CommunityStructure"]
      // });
      // echartRef.value.dispatchAction({
      //   type: 'downplay',
      //   seriesIndex: 0,
      //   dataIndex: [1, 2, 3, 4, 6, 15, 14]
      // });
      echartRef.value.dispatchAction({
        type: 'highlight',
        seriesIndex: 0,
        // dataIndex: [1, 2, 3, 4, 6, 14, 15,],
        dataIndex,
        // name: [params.name, "CommunityStructure"]
      })
      // setTimeout(() => {
      //   echartRef.value.dispatchAction({
      //     type: 'highlight',
      //     seriesIndex: 0,
      //     dataIndex: [1, 2, 3, 4, 6, 15, 14],
      //     // dataIndex: 4
      //     // name: [params.name, "CommunityStructure"]
      //   });
      // }, 3000)
      //  echartRef.value.dispatchAction({
      //   type: 'highlight',
      //   seriesIndex: 0,
      //   // dataIndex: [4, 6],
      //   dataIndex: 6
      //   // name: [params.name, "CommunityStructure"]
      // });
    })
  }
})
</script>

<template>
  <div>
    <div ref="echartDomRef" style="height: 700px" />
  </div>
</template>
