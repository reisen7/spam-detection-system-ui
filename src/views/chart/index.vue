<template>
<div class="iframe-container">  
      <iframe src="http://localhost:5173/#/" frameborder="0" allowfullscreen></iframe>  
    </div>
</template>
    
<script>
import { Bar } from '@antv/g2plot';
import axios from 'axios'; // 引入axios  


export default {
    name: "test-page",
    components: {},
    props: {},
    data() {
        return {
            stackedBarPlot: {},
            userData: [
                {
                    title: '中学生',
                    type: '休息时间',
                    value: 6,
                },
                {
                    title: '中学生',
                    type: '学习时间',
                    value: 5,
                },
                {
                    title: '中学生',
                    type: '上课时间',
                    value: 2,
                },
                {
                    title: '中学生',
                    type: '课后时间',
                    value: 10,
                },
                {
                    title: '小学生',
                    type: '休息时间',
                    value: 3,
                },
                {
                    title: '小学生',
                    type: '学习时间',
                    value: 3,
                },
                {
                    title: '小学生',
                    type: '上课时间',
                    value: 1,
                },
                {
                    title: '小学生',
                    type: '课后时间',
                    value: 5,
                },
                {
                    title: '大学生',
                    type: '休息时间',
                    value: 10,
                },
                {
                    title: '大学生',
                    type: '学习时间',
                    value: 11,
                },
                {
                    title: '大学生',
                    type: '上课时间',
                    value: 7,
                },
                {
                    title: '大学生',
                    type: '课后时间',
                    value: 15,
                }]
        }
    },
    methods: {
        init() {
            this.initAntvFx();// 使用获取到的数据创建堆叠条形图  


            console.log(process.env.VUE_APP_HADOOP_BASE_API)
            axios.get('http://localhost:8080/api/hdfs/read-csv?path=/output/hdfsJobByMonth/part-r-00000') // 发送GET请求到后台接口  
                .then(response => {
                    // 请求成功，response.data 包含了返回的数据  
                    this.userData = response.data;
                    this.initAntvFx();// 使用获取到的数据创建堆叠条形图  
                })
                .catch(error => {
                    // 请求失败，处理错误  
                    console.error('Error fetching data:', error);
                });

        },
        initAntvFx() {
            var data = this.userData;
            this.stackedBarPlot = new Bar('container', {
                data,
                isGroup: true,
                xField: 'value',
                yField: 'title',
                seriesField: 'type',
                dodgePadding: 4,
                label: {
                    position: 'middle', // 'left', 'middle', 'right'
                    layout: [
                        {
                            type: 'interval-adjust-position'
                        },
                        {
                            type: 'interval-hide-overlap'
                        },
                        {
                            type: 'adjust-color'
                        },
                    ],
                },
            });
            this.stackedBarPlot.render();
        },
    },
    mounted() {
        this.init();
    },
};
</script>
    
<style lang="scss" scoped>
.iframe-container {  
            position: relative;  
            width: 100%; /* 可以根据需要设置为具体宽度或百分比 */  
            height: 0; /* 高度初始设置为0，然后通过padding-bottom来设置高度 */  
            padding-bottom: 56.25%; /* 这个比例通常是16:9，你可以根据需要调整 */  
            overflow: hidden; /* 隐藏超出容器的内容 */  
        }  
          
        .iframe-container iframe {  
            position: absolute;  
            top: 0;  
            left: 0;  
            width: 100%;  
            height: 100%;  
            border: none; /* 移除边框 */  
        }  
</style>
    