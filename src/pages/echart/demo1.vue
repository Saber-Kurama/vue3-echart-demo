<script lang="ts" setup>
// 全量引入
import * as echarts from 'echarts'
import flareData from '../../data/flare.json'

defineOptions({ name: 'EchartDemo1' })
const echartDomRef = ref()
const echartRef = ref()
type EChartsOption = echarts.EChartsOption

flareData.children.forEach((
  datum: any,
  index: number,
) => {
  index % 2 === 0 && (datum.collapsed = true)
})
const option: EChartsOption = {
  tooltip: {
    trigger: 'item',
    triggerOn: 'mousemove',
  },
  series: [
    {
      type: 'tree',

      data: [flareData],

      top: '1%',
      left: '7%',
      bottom: '1%',
      right: '20%',

      symbolSize: 7,

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
        focus: 'descendant',
      },

      expandAndCollapse: true,
      animationDuration: 550,
      animationDurationUpdate: 750,
    },
  ],
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
  }
})
</script>

<template>
  <div>
    <div ref="echartDomRef" style="height: 600px" />
  </div>
</template>
