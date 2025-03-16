<template>
  <div id="app">
    <h1 class="main-title">邮件数据分析可视化大屏</h1>
    <div class="dashboard-container">
      <div class="row">
        <div class="col">
          <div class="chart-card">
            <h3 class="chart-title">📮 非垃圾邮件长度分布</h3>
            <div ref="nonSpamLengthDistributionChart" class="chart"></div>
          </div>
          <div class="chart-card">
            <h3 class="chart-title">📊 垃圾邮件高频词汇</h3>
            <div ref="spamTopWordsChart" class="chart"></div>
          </div>
        </div>
        <div class="col">
          <div class="chart-card">
            <h3 class="chart-title">📈 垃圾邮件平均词数分析</h3>
            <div ref="spamAverageWordCountChart" class="chart"></div>
          </div>
          <div class="chart-card">
            <h3 class="chart-title">📦 邮件类型分布</h3>
            <div ref="spamCountRatioChart" class="chart"></div>
          </div>
        </div>
        <div class="col">
          <div class="chart-card">
            <h3 class="chart-title">📬 非垃圾邮件高频词汇</h3>
            <div ref="nonSpamTopWordsChart" class="chart"></div>
          </div>
          <div class="chart-card">
            <h3 class="chart-title">📨 垃圾邮件长度分布</h3>
            <div ref="spamLengthDistributionChart" class="chart"></div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import * as echarts from 'echarts';

export default {
  name: 'EchartsDashboard',
  mounted () {
    echarts.registerTheme('dark', {
      backgroundColor: 'transparent',
      color: ['#37A2FF', '#32C5E9', '#67E0E3', '#9FE6B8', '#FFDB5C', '#ff9f7f']
    });
    this.renderCharts();
  },
  methods: {
    // 公共样式配置
    getCommonOptions (title) {
      return {
        title: {
          text: title,
          textStyle: {
            color: '#fff',
            fontSize: 16
          },
          left: 'center'
        },
        grid: {
          top: '20%',
          bottom: '15%',
          containLabel: true
        },
        tooltip: {
          trigger: 'axis',
          backgroundColor: 'rgba(0,0,0,0.8)',
          borderColor: '#333',
          textStyle: {
            color: '#fff'
          }
        }
      };
    },
    // 从 CSV 数据中提取 x 轴和 y 轴数据
    extractData (csvData) {
      const headers = csvData[0];
      const xData = [];
      const yData = [];
      for (let i = 1; i < csvData.length; i++) {
        xData.push(csvData[i][0]);
        yData.push(parseFloat(csvData[i][1]));
      }
      return { xData, yData };
    },
    // 渲染柱状图（修改后）
    async renderBarChart (chartRef, title, url) {
      try {
        const response = await fetch(url);
        const csvText = await response.text();
        const csvData = csvText.split('\n').filter(line => line.trim()).map(line => line.split(','));
        const { xData, yData } = this.extractData(csvData);

        const chart = echarts.init(chartRef, 'dark');
        const option = {
          ...this.getCommonOptions(title),
          xAxis: {
            type: 'category',
            data: xData,
            axisLabel: {
              color: '#999',
              rotate: 45
            },
            axisLine: {
              lineStyle: {
                color: '#666'
              }
            }
          },
          yAxis: {
            type: 'value',
            axisLabel: {
              color: '#999'
            },
            splitLine: {
              lineStyle: {
                color: '#333'
              }
            }
          },
          series: [{
            name: title,
            type: 'bar',
            data: yData,
            itemStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                { offset: 0, color: '#37A2FF' },
                { offset: 1, color: '#37A2FF77' }
              ]),
              borderRadius: [4, 4, 0, 0]
            },
            emphasis: {
              itemStyle: {
                shadowBlur: 10,
                shadowColor: 'rgba(55,162,255,0.5)'
              }
            },
            animationEasing: 'elasticOut'
          }]
        };
        chart.setOption(option);
      } catch (error) {
        console.error('Error rendering bar chart:', error);
      }
    },
    // 渲染折线图（修改后）
    async renderLineChart (chartRef, title, url) {
      try {
        const response = await fetch(url);
        const csvText = await response.text();
        const csvData = csvText.split('\n').filter(line => line.trim()).map(line => line.split(','));
        const { xData, yData } = this.extractData(csvData);

        const xDataN = xData.map((item) => item.replace(/"/g, ""));

        const chart = echarts.init(chartRef, 'dark');
        const option = {
          ...this.getCommonOptions(title),
          xAxis: {
            type: 'category',
            data: xDataN,
            axisLabel: {
              color: '#999'
            },
            axisLine: {
              lineStyle: {
                color: '#666'
              }
            }
          },
          yAxis: {
            type: 'value',
            axisLabel: {
              color: '#999'
            },
            splitLine: {
              lineStyle: {
                color: '#333'
              }
            }
          },
          series: [{
            name: title,
            type: 'line',
            data: yData,
            smooth: true,
            symbol: 'circle',
            symbolSize: 8,
            lineStyle: {
              width: 3,
              color: '#9FE6B8'
            },
            itemStyle: {
              color: '#9FE6B8',
              borderColor: '#fff',
              borderWidth: 2
            },
            areaStyle: {
              color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
                { offset: 0, color: '#9FE6B866' },
                { offset: 1, color: '#9FE6B811' }
              ])
            }
          }]
        };
        chart.setOption(option);
      } catch (error) {
        console.error('Error rendering line chart:', error);
      }
    },

    // 渲染饼图（修改后）
    async renderPieChart (chartRef, title, url) {
      try {
        const response = await fetch(url);
        const csvText = await response.text();
        const csvData = csvText.split('\n').filter(line => line.trim()).map(line => line.split(','));
        const data = csvData.slice(1).map(row => ({
          value: parseFloat(row[1].replace("\"", "")),
          name: row[0].replace(/"/g, '')
        }));

        const chart = echarts.init(chartRef, 'dark');
        const option = {
          ...this.getCommonOptions(title),
          tooltip: {
            trigger: 'item',
            formatter: '{a} <br/>{b}: {c} ({d}%)'
          },
          legend: {
            orient: 'vertical',
            right: '5%',
            top: 'middle',
            textStyle: {
              color: '#fff'
            }
          },
          series: [{
            name: title,
            type: 'pie',
            radius: ['40%', '70%'],
            center: ['35%', '50%'],
            roseType: 'radius',
            itemStyle: {
              borderRadius: 8,
              borderColor: '#1a1a1a',
              borderWidth: 2
            },
            label: {
              color: '#fff'
            },
            data: data,
            animationType: 'scale',
            animationEasing: 'elasticOut'
          }]
        };
        chart.setOption(option);
      } catch (error) {
        console.error('Error rendering pie chart:', error);
      }
    },

    // 渲染所有图表
    async renderCharts () {
      // 非垃圾邮件长度分布 - 柱状图
      await this.renderBarChart(
        this.$refs.nonSpamLengthDistributionChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/nonSpamLengthDistributionAnalysis/part-r-00000'
      );
      // 非垃圾邮件高频词汇 - 柱状图
      await this.renderBarChart(
        this.$refs.nonSpamTopWordsChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/nonSpamTopWordsAnalysis/part-r-00000'
      );
      // 垃圾邮件长度分布 - 柱状图
      await this.renderBarChart(
        this.$refs.spamLengthDistributionChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/spamLengthDistributionAnalysis/part-r-00000'
      );
      // 垃圾邮件高频词汇 - 柱状图
      await this.renderBarChart(
        this.$refs.spamTopWordsChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/spamTopWordsAnalysis/part-r-00000'
      );
      // 垃圾邮件平均词数分析 - 折线图
      await this.renderLineChart(
        this.$refs.spamAverageWordCountChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/spamAverageWordCountAnalysis/part-r-00000'
      );
      // 垃圾邮件与非垃圾邮件数量占比 - 饼图
      await this.renderPieChart(
        this.$refs.spamCountRatioChart,
        '',
        'http://localhost:8099/hdfs/read-csv?path=/output/spamCountAnalysis/part-r-00000'
      );
    }
  }
};
</script>
<style scoped>
#app {
  background-color: #1a1a1a;
  min-height: 100vh;
  padding: 20px;
}

.main-title {
  color: #fff;
  text-align: center;
  font-size: 2.5em;
  margin-bottom: 30px;
  text-shadow: 0 0 10px rgba(55, 162, 255, 0.5);
}

.dashboard-container {
  max-width: 1920px;
  margin: 0 auto;
}

.row {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 20px;
}

.chart-card {
  background: rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  padding: 20px;
  margin-bottom: 20px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  transition: transform 0.3s;
}

.chart-card:hover {
  transform: translateY(-5px);
}

.chart-title {
  color: #fff;
  margin: 0 0 15px;
  font-size: 16px;
  text-align: center;
}

.chart {
  height: 320px;
}

@media (max-width: 1200px) {
  .row {
    grid-template-columns: repeat(2, 1fr);
  }
}

@media (max-width: 768px) {
  .row {
    grid-template-columns: 1fr;
  }
}
</style>