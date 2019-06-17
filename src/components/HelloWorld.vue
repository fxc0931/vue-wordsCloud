<template>
  <div class="container">
    <el-row type="flex" justify="center">
      <el-col :xs="24" :sm="24" :md="20" :lg="12" :xl="12">
        <div v-for="(item,index) in adviceData" v-bind:key="index" :id="generateId(index)" class="position">
        </div>
      </el-col>
    </el-row>
  </div>
</template>

<script defer = "true">
import axios from 'axios'
export default {
  name: 'HelloWorld',
  data () {
    return {
      advice: [],
      dataSource: [{
        name: 'Macys',
        value: 6181,
        itemStyle: this.createRandomItemStyle()
      },
      {
        name: 'Amy Schumer',
        value: 4386,
        itemStyle: this.createRandomItemStyle()
      },
      {
        name: 'Jurassic World',
        value: 4055,
        itemStyle: this.createRandomItemStyle()
      }
      ],
      title: 'topic',
      myChart: 'myChart'
    }
  },
  computed: {
    adviceData: {
      get: function () {
        var dataArr = this.advice
        var dataSource = []
        var dataSourceArr = []
        for (let i = 0; i < dataArr.length; i++) {
          var dataArrOne = dataArr[i]
          for (let j = 0; j < dataArrOne.length; j++) {
            var stringArr = dataArrOne[j].split('*')
            // var reg = new RegExp('"', 'g')
            // stringArr[1] = stringArr[1].replace(reg, '')

            let newStr = stringArr[1].substring(1, stringArr[1].length - 1)
            var item = {
              name: newStr,
              value: stringArr[0] * 1000,
              textStyle: this.createRandomItemStyle()
            }
            dataSourceArr.push(item)
          }
          dataSource.push(dataSourceArr)
          dataSourceArr = []
        }
        return dataSource
      }
    }
  },
  watch: {
    adviceData: function (val) {
      // this.generateId()
      // this.drawLine()
    }
  },
  created () {
    axios.get('/static/dataSource.json')
      .then(response => {
        this.advice = response.data
      })
      .catch(error => {
        this.advice = 'There was an error: ' + error.message
      })
  },
  mounted () {
  },
  methods: {
    generateId: function (index) {
      let id = 'myChart' + index
      if (this.adviceData.length) {
        this.$nextTick(() => {
          this.myChart = id.toString()
          this.title = 'topic' + (index + 1)
          this.drawLine(this.title, this.adviceData[index], this.myChart)
        })
      }
      return id
    },
    createRandomItemStyle () {
      return {
        normal: {
          color: 'rgb(' + [
            Math.round(Math.random() * 160),
            Math.round(Math.random() * 160),
            Math.round(Math.random() * 160)
          ].join(',') + ')'
        }
      }
    },
    drawLine (title, dataSource, myChartId) {
      // 基于准备好的dom，初始化echarts实例
      let myChart = this.$echarts.init(document.getElementById(myChartId))
      // 绘制图表
      let option = {
        title: {
          text: title
        },
        tooltip: {
          show: true
        },
        series: [{
          name: title,
          type: 'wordCloud',
          size: ['100%', '100%'],
          textRotation: [0, 45, 180, -45],
          textPadding: 2,
          autoSize: {
            enable: true,
            minSize: 14
          },
          data: dataSource
        }]
      }
      // 为echarts对象加载数据
      myChart.setOption(option)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .position {
    height: 367px;
  }
</style>
