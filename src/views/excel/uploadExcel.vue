<template>
  <div class="app-container">
    <!-- 上传excel文件 -->
    <upload-excel-component @on-selected-file='selected'></upload-excel-component>
    <!-- excel文件 - 列表 -->
    <!--  highlight-current-row 单选 - 状态背景色 -->
    <div style="width: 100%;float: left;">
      <div :style="tableData.length?'width: 1000px;margin: 0 auto;':'width: 100%;'">
        <el-table 
          ref="multipleTable" 
          :data="tableData" 
          :height="tableData.length?'512':''" 
          border 
          style="margin-top:20px;"
          :style="tableData.length?'width: 129px;float: left;':'width: 100%;'"
          @selection-change="handleSelectionChange">
          <el-table-column v-if="tableData.length" type="selection" align="center"></el-table-column>
          <el-table-column v-for='item of tableHeader' :prop="item" :label="item" :key='item'>
          </el-table-column>
        </el-table>
        <!-- 点击ProjectName属性 - 显示图表 -->
        <el-row v-if="multipleSelection.length" style="background:#fff;padding:32px 16px 0;margin-bottom:32px;width: 87%;float: left;">
          <line-chart :chart-data="lineChartData"></line-chart>
        </el-row>
      </div>
    </div>
  </div>
</template>

<script>
import UploadExcelComponent from '@/components/UploadExcel/index.vue'
import LineChart from '@/components/UploadExcel/LineChart.vue'

export default {
  name: 'uploadExcel',
  components: {
    UploadExcelComponent,
    LineChart
  },
  data() {
    return {
      tableData: [],
      tableHeader: [],
      lineChartData: {},
      multipleSelection: []
    }
  },
  /*
  computed: {
    // 这个计算属性用来拼接参数
    lineChartData() {
      const result = {}
      // 时间参数
      result.timeData = this.tableHeader.slice(1, 8)
      console.log(result.timeData)
      // 遍历所有线的数据
      // const index = this.multipleSelection.length
      // const lineDatas = 'linename' + index
      result['lineDatas1'] = [45, 78, 30, 10, 2, 13]
      result['lineDatas2'] = [145, 178, 130, 110, 12, 113]

      // this.lineDatas.forEach((data, index) => {
      //   result['lineData' + index] = data
      // })
      return result
    }
  },
  */
  created() {
  },
  mounted() {
  },
  methods: {
    selected(data) {
      this.tableData = data.results
      this.tableHeader = data.header
      this.$nextTick(function() {
        this.checked([this.tableData[0], this.tableData[1], this.tableData[2], this.tableData[3], this.tableData[4]]) // 每次更新了数据，触发这个函数即可。
      })
    },
    checked(rows) {
      // 首先el-table添加 ref="multipleTable" 引用标识
      rows.forEach(row => {
        this.$refs.multipleTable.toggleRowSelection(row, true)
      })
    },
    handleSelectionChange(val) {
      console.log(val)
      const timeData = this.tableHeader.slice(1, 8)
      const titleVal = val
      if (titleVal.length !== 0) {
        let titleKey = []
        for (let k = 0; k < titleVal.length; k++) {
          titleKey += titleVal[k].ProjectName + ','
        }
        titleKey = titleKey.substring(0, titleKey.length - 1)
        const titleData = titleKey.split(',')
        const data = { timeData, titleData }
        val.forEach((line, index) => {
          let arr = timeData.map(time => {
            return line[time] || null
          })
          if (arr[0] !== null) {
            for (let i = 0; i < arr.length; i++) {
              if (arr[i] === null) {
                var nullindex = i
                if (nullindex < 3) {
                  arr[i] = arr[0]
                } else if (nullindex < 4) {
                  arr[i] = arr[2]
                } else if (nullindex < 5) {
                  arr[i] = arr[3]
                } else if (nullindex < 6 || 7) {
                  arr[i] = arr[4]
                }
              }
            }
          } else {
            arr = []
          }
          data[`lineData${index}`] = arr
        })
        console.log(data)
        this.lineChartData = data
      }
      this.multipleSelection = val
    }
  }
}
</script>
