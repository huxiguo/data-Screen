<template>
  <div class="dataScreen-container">
    <div class="dataScreen" ref="dataScreenRef">
      <!-- 头部 -->
      <div class="dataScreen-header">
        <div class="header-lf">
          <span class="header-screening">首页</span>
        </div>
        <div class="header-ct">
          <div class="header-ct-title">
            <span>可视化大数据展示平台</span>
            <div class="header-ct-warning">预警信息(2)</div>
          </div>
        </div>
        <div class="header-rg">
          <span class="header-download">统计报告</span>
          <span class="header-time">当前时间：{{ time }}</span>
        </div>
      </div>
      <!-- 主体区域 -->
      <div class="dataScreen-main">
        <!-- 左侧分区 -->
        <div class="dataScreen-lf">
          <!-- 订单图 -->
          <div class="dataScreen-top">
            <div class="dataScreen-main-title">
              <span>实时订单统计</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <RealTimeAccessChart ref="RealTimeAccessRef" />
            </div>
          </div>
          <!-- 比例图 -->
          <div class="dataScreen-center">
            <div class="dataScreen-main-title">
              <span>男女比例</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <MaleFemaleRatioChart ref="MaleFemaleRatioRef" />
            </div>
          </div>
          <!-- 年龄图 -->
          <div class="dataScreen-bottom">
            <div class="dataScreen-main-title">
              <span>年龄比例</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <AgeRatioChart ref="AgeRatioRef" />
            </div>
          </div>
        </div>
        <!-- 中间分区 -->
        <div class="dataScreen-ct">
          <!-- 地图 -->
          <div class="dataScreen-map">
            <div class="dataScreen-map-title">实时航空流量</div>
            <mapChart ref="MapChartRef" />
          </div>
          <!-- 预测 -->
          <div class="dataScreen-cb">
            <div class="dataScreen-main-title">
              <span>未来30天趋势图</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <OverNext30Chart ref="OverNext30Ref" />
            </div>
          </div>
        </div>
        <!-- 右侧分区 -->
        <div class="dataScreen-rg">
          <!-- 排行榜 -->
          <div class="dataScreen-top">
            <div class="dataScreen-main-title">
              <span>热门游戏排行</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <HotPlateChart ref="HotPlateRef" />
            </div>
          </div>
          <!-- 对比 -->
          <div class="dataScreen-center">
            <div class="dataScreen-main-title">
              <span>玩家活跃度对比</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <AnnualUseChart ref="AnnualUseRef" />
            </div>
          </div>
          <!-- 饼状 -->
          <div class="dataScreen-bottom">
            <div class="dataScreen-main-title">
              <span>游戏充值数据统计</span>
              <img src="./images/dataScreen-title.png" alt="" />
            </div>
            <!-- chart区域 -->
            <div class="dataScreen-main-chart">
              <PlatformSourceChart ref="PlatformSourceRef" />
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts" name="dataScreen">
import { ref, Ref, onMounted, onBeforeUnmount } from 'vue'
import { randomNum } from '@/utils'
import { useTime } from '@/hooks/useTime'
import { ECharts } from 'echarts'
import mapChart from './components/chinaMapChart.vue'
import AgeRatioChart from './components/AgeRatioChart.vue'
import AnnualUseChart from './components/AnnualUseChart.vue'
import HotPlateChart from './components/HotPlateChart.vue'
import MaleFemaleRatioChart from './components/MaleFemaleRatioChart.vue'
import OverNext30Chart from './components/OverNext30Chart.vue'
import PlatformSourceChart from './components/PlatformSourceChart.vue'
import RealTimeAccessChart from './components/RealTimeAccessChart.vue'

const dataScreenRef = ref<HTMLElement | null>(null)

onMounted(() => {
  // 初始化时为外层盒子加上缩放属性，防止刷新界面时就已经缩放
  if (dataScreenRef.value) {
    dataScreenRef.value.style.transform = `scale(${getScale()}) translate(-50%, -50%)`
    dataScreenRef.value.style.width = `1920px`
    dataScreenRef.value.style.height = `1080px`
  }
  // 初始化 echarts
  initCharts()
  // 为浏览器绑定事件
  window.addEventListener('resize', resize)
})

// 根据浏览器大小推断缩放比例
const getScale = (width = 1920, height = 1080) => {
  let ww = window.innerWidth / width
  let wh = window.innerHeight / height
  return ww < wh ? ww : wh
}

// 监听浏览器 resize 事件
const resize = () => {
  if (dataScreenRef.value) {
    dataScreenRef.value.style.transform = `scale(${getScale()}) translate(-50%, -50%)`
  }
  // 使用了 scale 的echarts其实不需要需要重新计算缩放比例
  Object.values(dataScreen).forEach(chart => {
    chart && chart.resize()
  })
}

// 声明echarts实例
interface ChartProps {
  [key: string]: ECharts | null
}
const dataScreen: ChartProps = {
  chart1: null,
  chart2: null,
  chart3: null,
  chart4: null,
  chart5: null,
  chart6: null,
  chart7: null,
  mapChart: null
}

// 获取子组件的ref
interface ChartExpose {
  initChart: (params: any) => ECharts
}
const RealTimeAccessRef = ref<ChartExpose>()
const AgeRatioRef = ref<ChartExpose>()
const AnnualUseRef = ref<ChartExpose>()
const HotPlateRef = ref<ChartExpose>()
const MaleFemaleRatioRef = ref<ChartExpose>()
const OverNext30Ref = ref<ChartExpose>()
const PlatformSourceRef = ref<ChartExpose>()
const MapChartRef = ref<ChartExpose>()

// 初始化 charts参数
// 年龄数据
let ageData = [
  {
    value: 200,
    name: '10岁以下',
    percentage: '16%'
  },
  {
    value: 110,
    name: '10 - 18岁',
    percentage: '8%'
  },
  {
    value: 150,
    name: '18 - 30岁',
    percentage: '12%'
  },
  {
    value: 310,
    name: '30 - 40岁',
    percentage: '24%'
  },
  {
    value: 250,
    name: '40 - 60岁',
    percentage: '20%'
  },
  {
    value: 260,
    name: '60岁以上',
    percentage: '20%'
  }
]
let hotData = [
  {
    value: 79999,
    name: '原神',
    percentage: '80%',
    maxValue: 100000
  },
  {
    value: 59999,
    name: '王者荣耀',
    percentage: '60%',
    maxValue: 100000
  },
  {
    value: 49999,
    name: '和平精英',
    percentage: '50%',
    maxValue: 100000
  },
  {
    value: 39999,
    name: '英雄联盟',
    percentage: '40%',
    maxValue: 100000
  },
  {
    value: 29992,
    name: '部落冲突',
    percentage: '30%',
    maxValue: 100000
  }
]
let platFromData = [
  {
    value: 40,
    name: '王者荣耀',
    percentage: '40%'
  },
  {
    value: 10,
    name: '和平精英',
    percentage: '10%'
  },
  {
    value: 20,
    name: '原神',
    percentage: '20%'
  },
  {
    value: 30,
    name: '部落冲突',
    percentage: '30%'
  }
]
let annualData = [
  {
    label: '原神',
    value: ['1840', '900', '1200', '5000', '3000', '1000', '3500', '4000', '2000', '5100', '3500', '1080']
  },
  {
    label: '王者荣耀',
    value: ['1108', '5009', '3066', '1062', '3080', '1023', '3021', '1058', '3052', '4074', '1054', '2002']
  },
  {
    label: '和平精英',
    value: ['5048', '2059', '1013', '5000', '3009', '5012', '2030', '4009', '2008', '4200', '3103', '1056']
  }
]
let mapData = [
  {
    fromName: '北京',
    toName: '上海',
    coords: [
      [116.4551, 40.2539],
      [121.4648, 31.2891]
    ]
  },
  {
    fromName: '上海',
    toName: '北京',
    coords: [
      [121.4648, 31.2891],
      [116.4551, 40.2539]
    ]
  },
  {
    fromName: '北京',
    toName: '广州',
    coords: [
      [116.4551, 40.2539],
      [113.5107, 23.2196]
    ]
  },
  {
    fromName: '广州',
    toName: '北京',
    coords: [
      [113.5107, 23.2196],
      [116.4551, 40.2539]
    ]
  },
  {
    fromName: '北京',
    toName: '成都',
    coords: [
      [116.4551, 40.2539],
      [103.9526, 30.7617]
    ]
  },
  {
    fromName: '成都',
    toName: '北京',
    coords: [
      [103.9526, 30.7617],
      [116.4551, 40.2539]
    ]
  },
  {
    fromName: '成都',
    toName: '新疆维吾尔自治区',
    coords: [
      [103.9526, 30.7617],
      [85.294711, 41.371801]
    ]
  },
  {
    fromName: ' 新疆维吾尔自治区',
    toName: '成都',
    coords: [
      [85.294711, 41.371801],
      [103.9526, 30.7617]
    ]
  },
  {
    fromName: ' 新疆维吾尔自治区',
    toName: '台湾',
    coords: [
      [85.294711, 41.371801],
      [121.56517, 25.037798]
    ]
  },
  {
    fromName: '南昌',
    toName: '赣州',
    coords: [
      [115.892151, 28.676493],
      [114.940278, 25.85097]
    ]
  }
]
const bfb = ref(0.05)
setInterval(() => {
  bfb.value += 0.01
  if (bfb.value > 1) {
    bfb.value = 0.05
  }
  changeData()
}, 1000)
// 刷新数据
const changeData = () => {
  dataScreen.chart1 = RealTimeAccessRef.value?.initChart(bfb.value) as ECharts
  dataScreen.chart6 = OverNext30Ref.value?.initChart({
    unit: ['访问量'],
    data: new Array(30).fill('').map(val => {
      val = randomNum(1, 20000)
      return val
    })
  }) as ECharts
}
// 初始化 echarts
const initCharts = (): void => {
  dataScreen.chart1 = RealTimeAccessRef.value?.initChart(bfb.value) as ECharts
  dataScreen.chart2 = AgeRatioRef.value?.initChart(ageData) as ECharts
  dataScreen.chart3 = AnnualUseRef.value?.initChart({
    data: annualData,
    unit: annualData.map(val => val.label),
    columns: ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '12'],
    colors: ['#FFA600', '#007AFE', '#FF4B7A']
  }) as ECharts
  dataScreen.chart4 = HotPlateRef.value?.initChart({
    data: hotData,
    colors: ['#1089E7', '#F57474', '#56D0E3', '#F8B448', '#8B78F6']
  }) as ECharts
  dataScreen.chart5 = MaleFemaleRatioRef.value?.initChart({
    man: 0.13,
    woman: 0.87
  }) as ECharts
  dataScreen.chart6 = OverNext30Ref.value?.initChart({
    unit: ['访问量'],
    data: new Array(30).fill('').map(val => {
      val = randomNum(1, 20000)
      return val
    })
  }) as ECharts
  dataScreen.chart7 = PlatformSourceRef.value?.initChart({
    data: platFromData,
    colors: ['#078dbc', '#6ad40b', '#6172fc', '#1786ff', '#ffbe2f', '#4dc89d', '#b797df', '#ffd3aa']
  }) as ECharts
  dataScreen.mapChart = MapChartRef.value?.initChart(mapData) as ECharts
}

// 获取当前时间
const { nowTime } = useTime()
let timer: NodeJS.Timer | null = null
let time: Ref<string> = ref(nowTime.value)
timer = setInterval(() => {
  time.value = useTime().nowTime.value
}, 1000)

// 销毁时触发
onBeforeUnmount(() => {
  window.removeEventListener('resize', resize)
  clearInterval(timer!)
  Object.values(dataScreen).forEach(val => val?.dispose())
})
</script>
<style lang="scss" scoped>
@import './index.scss';
</style>
