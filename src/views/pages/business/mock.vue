<template>
<div class="iframe-container">  
      <iframe src="http://localhost:5173/#/mock" frameborder="0" allowfullscreen></iframe>  
    </div>
</template>

<script>
// import Mock from 'mockjs';
// import dayjs from 'dayjs'
// import axios from 'axios';

// const doMock = () => {
//   const data = []
  
//   // for (item of type) {
//   for (let month = 0; month < 12; month++) {
//     for (let hour = 0; hour < 24; hour++) {
//       let item;
//       if (hour < 7) {
//         item = Mock.mock({
//           "时间": dayjs(new Date(2023, month, 2, hour, 0, 0)).format('YYYY-MM-DD HH:mm:ss'),
//           "南京路|10-20": 1,
//           "沈营路|5-10": 1,
//           "白云路|7-10": 1,
//           "中央大街|0-15": 1,
//           "长安路|10-15": 1,
//           // "车流量|10-20": 1,
//         })
//       } else if (hour < 10) {
//         item = Mock.mock({
//           "时间": dayjs(new Date(2023, month, 2, hour, 0, 0)).format('YYYY-MM-DD HH:mm:ss'),
//           "南京路|70-100": 1,
//           "沈营路|90-120": 1,
//           "白云路|60-80": 1,
//           "中央大街|110-130": 1,
//           "长安路|80-100": 1,
//         })
//       } else if (hour < 17) {
//         item = Mock.mock({
//           "时间": dayjs(new Date(2023, month, 2, hour, 0, 0)).format('YYYY-MM-DD HH:mm:ss'),
//           "南京路|10-20": 1,
//           "沈营路|5-10": 1,
//           "白云路|7-10": 1,
//           "中央大街|0-15": 1,
//           "长安路|5-20": 1,
//         })
//       } else if (hour < 20) {
//         item = Mock.mock({
//           "时间": dayjs(new Date(2023, month, 2, hour, 0, 0)).format('YYYY-MM-DD HH:mm:ss'),
//           "南京路|60-130": 1,
//           "沈营路|70-120": 1,
//           "白云路|90-120": 1,
//           "中央大街|100-110": 1,
//           "长安路|99-140": 1,
//         })
//       } else {
//         item = Mock.mock({
//           "时间": dayjs(new Date(2023, month, 2, hour, 0, 0)).format('YYYY-MM-DD HH:mm:ss'),
//           "南京路|10-20": 1,
//           "沈营路|5-10": 1,
//           "白云路|7-10": 1,
//           "中央大街|0-15": 1,
//           "长安路|0-15": 1,
//         })
//       }
      
//       data.push(item)
//     }
//   }
//   // }
  
//   return data
// }

// const doUpload = () => {
//   const data = doMock()
//   axios.post('/api/hdfs/upload-by-json', { 
//     data, 
//     name: "default",
//     header: ['时间', ...window.CONFIG.dataHeader]
//   }).then(res => {
//     console.log(res)
//   })
// }
export default{
  
}

</script>

<style scoped>
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