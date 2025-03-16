<template>
    <div>
        <!-- <button @click="job1">爆款预测</button> -->
        <div ref="chartRef"></div>


    </div>
</template>
  
<script>
import { Chart } from '@antv/g2';

export default {
    name: 'ChartComponent', // 组件名称  
    data() {
        return {
            chart: null, // 用于存储图表实例  
        };
    },
    mounted() {
        this.initChart();
    },
    methods: {
        initChart() {
            const chartRef = this.$refs.chartRef; // 使用 this.$refs 引用 DOM 元素  

            const keys = window.CONFIG.dataHeader;

            console.log(keys)
            this.chart = new Chart({
                container: chartRef,
                autoFit: true,
            });

            // ... 图表配置代码保持不变 ...  

            //   chart.coordinate({ type: 'theta' });


            this.chart
                .title({
                    title: `爆款预测`,
                })
                // .interval()
                .data({
                    type: 'fetch',
                    value: process.env.VUE_APP_HADOOP_BASE_API + '/hdfs/read-csv/yuce?path=/output/hdfsJobByYear/part-r-00000',
                    format: 'csv',
                    autoType: true,
                    transform: [

                        {
                            type: 'map',
                            callback: (val) => { val['年'] = val['时间'] + '年'; return val; },
                        },
                        // {
                        //   type: 'custom',
                        //   callback: (data) => {
                        //     const result = keys.map(key => {
                        //       const re = data.reduce((currentSum, item) =>  currentSum + item[key] , 0)
                        //       return data
                        //     })
                        //     return result
                        //   }
                        // },
                        {
                            type: 'fold',
                            fields: window.CONFIG.dataHeader,
                            key: window.CONFIG.chartX,
                            value: window.CONFIG.chartY,
                        },
                        {
                            type: 'sortBy',
                            fields: ['时间']
                        },
                        {
                            type: 'log'
                        },
                    ],

                })
                .encode('x', '时间')
                .encode('y', window.CONFIG.chartY)
                .encode('color', window.CONFIG.chartX)
                .scale('x', {
                    range: [0, 1],
                })
                .scale('y', {
                    nice: true,
                })
                .axis('x', { title: null, line: true, tick: true })
                .axis('y', {
                    title: null,
                    line: true,
                })
            this.chart.line().encode('shape', 'smooth');
            this.chart.render();

        },
    },
    // 如果需要从父组件传递 prop，可以这样定义  

};




</script>
  
<style scoped>
:root {
    font-family: Inter, system-ui, Avenir, Helvetica, Arial, sans-serif;
    line-height: 1.5;
    font-weight: 400;

    color-scheme: light dark;
    color: rgba(255, 255, 255, 0.87);
    background-color: #242424;

    font-synthesis: none;
    text-rendering: optimizeLegibility;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

a {
    font-weight: 500;
    color: #646cff;
    text-decoration: inherit;
}

a:hover {
    color: #535bf2;
}

body {
    margin: 0;
    /* display: flex; */
    place-items: center;
    min-width: 320px;
    width: 100%;
    min-height: 100vh;
}

h1 {
    font-size: 3.2em;
    line-height: 1.1;
}

button {
    border-radius: 8px;
    border: 1px solid transparent;
    padding: 0.6em 1.2em;
    font-size: 1em;
    font-weight: 500;
    font-family: inherit;
    background-color: #fffbfb;
    cursor: pointer;
    transition: border-color 0.25s;
}

button:hover {
    border-color: #646cff;
}

button:focus,
button:focus-visible {
    outline: 4px auto -webkit-focus-ring-color;
}

/* .card {
  padding: 2em;
} */

/* #app {
  max-width: 1280px;
  margin: 0 auto;
  padding: 2rem;
  text-align: center;
} */

@media (prefers-color-scheme: light) {
    :root {
        color: #213547;
        background-color: #ffffff;
    }

    a:hover {
        color: #747bff;
    }

    button {
        background-color: #f9f9f9;
    }
}
</style>