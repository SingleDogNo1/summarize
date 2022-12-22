<template>
  <div class="Gantt flex" ref="Gantt"></div>
</template>

<script lang="ts">
export default {
  name: 'Gantt'
}
</script>

<script lang="ts" setup>
import { ref, onMounted } from 'vue'
import * as echarts from 'echarts'
import dayjs from 'dayjs'

const Gantt = ref()

const projects = new Array(9).fill('')
const projectData = [
  {
    value: [
      {
        index: 0,
        startTime: `2022-1-1`,
        endTime: `2022-5-31`,
        status: 'done',
        content: '赵楼智能化改造WEB / APP'
      }
    ]
  },
  {
    value: [
      {
        index: 1,
        startTime: `2022-1-1`,
        endTime: `2022-1-31`,
        status: 'done',
        content: '万利APP'
      }
    ]
  },
  {
    value: [
      {
        index: 2,
        startTime: `2022-2-1`,
        endTime: `2022-8-31`,
        status: 'done',
        content: '淮南停送电云平台APP'
      }
    ]
  },

  {
    value: [
      {
        index: 3,
        startTime: `2022-1-18`,
        endTime: `2022-2-20`,
        status: 'done',
        content: '表格 & 表单组件开发'
      }
    ]
  },
  {
    value: [
      {
        index: 4,
        startTime: `2022-5-1`,
        endTime: `2022-8-20`,
        status: 'done',
        content: '赵楼设备运行统计 & 设备拆装'
      }
    ]
  },
  {
    value: [
      {
        index: 5,
        startTime: `2022-6-1`,
        endTime: `2022-6-30`,
        status: 'done',
        content: '石拉乌素改造'
      }
    ]
  },
  {
    value: [
      {
        index: 6,
        startTime: `2022-9-1`,
        endTime: `2022-9-30`,
        status: 'done',
        content: '云平台合并到赵楼'
      }
    ]
  },
  {
    value: [
      {
        index: 7,
        startTime: `2022-10-8`,
        endTime: `2022-10-30`,
        status: 'done',
        content: '中阳鑫隆 APP 端改造'
      }
    ]
  },
  {
    value: [
      {
        index: 8,
        startTime: `2022-10-8`,
        endTime: `2022-12-31`,
        status: 'during',
        content: '技术管理改造'
      }
    ]
  }
]

onMounted(() => {
  const myChart = echarts.init(Gantt.value, null, {
    width: 900,
    height: 500
  })

  myChart.setOption({
    color: '#0A8BFF',
    backgroundColor: '#fff',
    title: {},
    tooltip: {
      trigger: 'item',
      show: true,
      hideDelay: 100,
      backgroundColor: 'rgba(255,255,255,1)', // 背景颜色（此时为默认色）
      borderRadius: 5, // 边框圆角
      textStyle: {
        color: '#000'
      },
      formatter: function (params) {
        const item = params.data.value[0]
        return (
          item.content +
          '<br/>' +
          (item.status === 'done'
            ? '<span style="color:#4dc394;">已完成</span>'
            : '<span style="color:#e5835b;">进行中</span>') +
          '<br/>' +
          item.startTime +
          ' 到 ' +
          item.endTime
        )
      }
    },
    legend: {
      top: '1%',
      itemWidth: 16,
      itemHeight: 16,
      show: true,
      textStyle: {
        color: 'rgba(0, 0, 0, 0.45)',
        fontSize: 14
      }
    },
    grid: {
      // 绘图网格
      left: '2%',
      right: '3%',
      top: '10%',
      bottom: '10%',
      containLabel: true
    },
    xAxis: {
      type: 'time',
      position: 'top',
      interval: 1000 * 60 * 60 * 24 * 30,
      min: `2022-1-1`,
      max: `2022-12-31`,
      axisLabel: {
        formatter: function (value) {
          return dayjs(value).format('YYYY-MM')
        },
        textStyle: {
          color: 'rgba(0,0,0,0.65)',
          fontSize: 14
        }
      },
      axisLine: {
        lineStyle: {
          color: '#e5e5e5'
        },
        onZero: false
      },
      splitLine: {
        show: true,
        lineStyle: {
          type: 'dashed'
        }
      }
    },
    yAxis: {
      inverse: true,
      type: 'category',
      axisLine: {
        lineStyle: {
          color: '#e5e5e5'
        }
      },
      data: projects,
      axisLabel: {
        textStyle: {
          color: 'rgba(0, 0, 0, 0.65)', // 刻度颜色
          fontSize: 14 // 刻度大小
        }
      }
    },
    series: [
      {
        type: 'custom',
        clickable: false,
        renderItem: function (params, api) {
          // 开发者自定义的图形元素渲染逻辑，是通过书写 renderItem 函数实现的
          var categoryIndex = api.value(0).index // 这里使用 api.value(0) 取出当前 dataItem 中第一个维度的数值。
          var start = api.coord([api.value(0).startTime, categoryIndex]) // 这里使用 api.coord(...) 将数值在当前坐标系中转换成为屏幕上的点的像素值。
          var end = api.coord([api.value(0).endTime, categoryIndex])

          var height = 40
          return {
            type: 'rect', // 表示这个图形元素是矩形。还可以是 'circle', 'sector', 'polygon' 等等。
            shape: echarts.graphic.clipRectByRect(
              {
                // 矩形的位置和大小。
                x: start[0],
                y: start[1] - height / 2,
                width: end[0] - start[0],
                height: 40
              },
              {
                // 当前坐标系的包围盒。
                x: params.coordSys.x,
                y: params.coordSys.y,
                width: params.coordSys.width,
                height: params.coordSys.height
              }
            ),
            style: api.style()
          }
        },
        label: {
          normal: {
            show: true,
            position: 'insideBottom',
            formatter: function (params) {
              return params.value[0].content
            },
            textStyle: {
              align: 'center',
              fontSize: 14,
              fontWeight: '400',
              lineHeight: '30'
            }
          }
        },
        encode: {
          x: 1, // data 中『维度1』对应到 X 轴
          y: 0 // 把"维度0"映射到 Y 轴。
        },

        itemStyle: {
          normal: {
            color: function (params) {
              if (params.value[0].status === 'done') return '#4dc394'
              else return '#e5835b'
            }
          }
        },
        data: projectData
      }
    ]
  })

  window.onresize = function () {
    myChart.resize()
  }
})
</script>

<style>
.Gantt {
  width: 100%;
  height: 500px;
}
</style>
