<template>
  <div :class="className" :style="{height:height,width:width}"></div>
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'

export default {
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '500px'
    },
    autoResize: {
      type: Boolean,
      default: true
    },
    chartData: {
      type: Object
    }
  },
  data() {
    return {
      chart: null
    }
  },
  mounted() {
    this.initChart()
    if (this.autoResize) {
      this.__resizeHanlder = debounce(() => {
        if (this.chart) {
          this.chart.resize()
        }
      }, 100)
      window.addEventListener('resize', this.__resizeHanlder)
    }

    // 监听侧边栏的变化
    const sidebarElm = document.getElementsByClassName('sidebar-container')[0]
    sidebarElm.addEventListener('transitionend', this.__resizeHanlder)
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    if (this.autoResize) {
      window.removeEventListener('resize', this.__resizeHanlder)
    }

    const sidebarElm = document.getElementsByClassName('sidebar-container')[0]
    sidebarElm.removeEventListener('transitionend', this.__resizeHanlder)

    this.chart.dispose()
    this.chart = null
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        // debugger
        this.setOptions(val)
      }
    }
  },
  methods: {
    setOptions({ timeData, titleData, ...datas } = {}) {
      const series = []
      const colors = ['#F00', '#ff0', '#de8909', '#0a19b5', '#67c23a', '#21baf1', '#000', '#eb21f1', '#B4B4AF', '#9B4D0D', '#FFB7B9', '#C9E00F', '#FFC136', '#FF1789', '#34FFED', '#71FF34']
      let index = 0
      for (const key in datas) {
        const color = colors[index % colors.length]
        index++
        console.log(color)
        series.push({
          name: titleData[index - 1] + ' 数量',
          smooth: true,
          type: 'line',
          itemStyle: {
            normal: {
              color,
              lineStyle: {
                color,
                width: 2
              }
            }
          },
          data: datas[key],
          animationDuration: 2800,
          animationEasing: 'cubicInOut'
        })
      }
      console.log(series)
      this.chart.setOption({
        xAxis: [{
          data: timeData,
          boundaryGap: false,
          axisTick: {
            show: false
          }
        }],
        grid: {
          top: 5,
          left: '3%',
          right: '4%',
          bottom: '2%',
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross'
          },
          padding: [5, 10]
        },
        yAxis: {
          axisTick: {
            show: false
          }
        },
        // legend: {
        //   data: ['expected', 'actual']
        // },
        series
      }, true)
    },
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.setOptions(this.chartData)
    }
  }
}
</script>
