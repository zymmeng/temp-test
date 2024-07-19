<script setup>
import { ref, onMounted } from "vue";
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
    // data:
    // min: 35,
    //   max: 40,
  },
  yAxis: {
    type: "value",
  },
  series: [
    {
      data: pushData.value || [],
      type: "line",
      smooth: true,
    },
  ],
};
const conform = () => {
  pushData.value.push([time.value, temperature.value]);
  option.series[0].data = pushData.value;
  chartViewer.value.setOption(option);
  localStorage.setItem("temperature", JSON.stringify(pushData.value));
};
onMounted(() => {
  chartViewer.value = echarts.init(mychart.value, null, {
    renderer: "svg",
    passive: true,
  });
  chartViewer.value.setOption(option);
});
const count = ref(0);
</script>

<template>
  <div style="width: 200px; display: flex;position: absolute;top: 0;left: 0;">
    <div>时间</div>
    <el-input v-model="time"></el-input>
    <div>温度</div>
    <el-input v-model="temperature"></el-input>
    <el-button @click="conform">确定</el-button>
  </div>
  <div ref="mychart" id="main" class="chart"></div>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
.chart {
  position: absolute;
  width: 200px;
  height:200px;
  left: 0;
  top: 40px;
  padding-left: 10px;
  padding-right: 10px;
  /* background-color: red; */
}
</style>
