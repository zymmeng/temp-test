<script setup>
import { ref, onMounted, nextTick } from "vue";
import * as echarts from "echarts";
const mychart = ref();
const time = ref();
const temperature = ref();
// 基于准备好的dom，初始化echarts实例

// 绘制图表

defineProps({
  msg: String,
});

let chartViewer = ref();
const pushData = ref(JSON.parse(localStorage.getItem("temperature")) || []);
let option = {
  xAxis: {
    type: "category",
    axisTick: {
                show: true,
                alignWithLabel: true,
                  inside: true,
            },
    // data:

    //   max: 40,
  },
  yAxis: {
    type: "value",
    min: 36,
    max: 39,
    splitLine: {
            show: true // 是否显示 y 轴分割线
        },
        axisLine: {
            show: true // 是否显示 y 轴线
        },
  },
  grid: {
    top: "30px",
    left: 50,
    bottom: 20,
    right: 10,
  },
  dataZoom: [
    {
      type: "inside",
      // start:0,
      // end:100
    },
  ],
  series: [
    {
      data: pushData.value || [],
      type: "line",
      smooth: true,
      label: {
            show: true, // 显示拐点标签
            position: 'top' // 拐点标签位置
        },
    },
  ],
};
const conform = () => {
  pushData.value.push([time.value, temperature.value]);
  option.series[0].data = pushData.value;
  chartViewer.value.setOption(option);
  localStorage.setItem("temperature", JSON.stringify(pushData.value));
  time.value = "";
  temperature.value = "";
};
onMounted(() => {
  chartViewer.value = echarts.init(mychart.value, null, {
    renderer: "svg",
    passive: true,
  });
  chartViewer.value.setOption(option);
});
const clear = () => {
  localStorage.clear();
  nextTick(() => {
    pushData.value = [];
    option.series[0].data = pushData.value;
    chartViewer.value.setOption(option);
  });
};
const count = ref(0);
</script>

<template>
  <div style="width: 200px; display: flex; position: absolute; top: 0; left: 20px">
    <div>时间</div>
    <el-input v-model="time"></el-input>
    <div>温度</div>
    <el-input v-model="temperature"></el-input>

    <div style="display: flex;flex-direction: column;">

      <el-button @click="conform">确定</el-button>
      <el-button @click="clear">清除</el-button>
    </div>
  </div>

  <div ref="mychart" id="main" class="chart">
   
  </div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
.chart {
  position: absolute;
  width: 300px;
  height: 200px;
  left: 0;
  top:60px;
  padding-left: 10px;
  padding-right: 10px;
  /* background-color: red; */
}
</style>
