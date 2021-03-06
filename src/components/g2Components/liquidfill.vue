/*
 * @Author: wupeiwen javapeiwen2010@gmail.com
 * @Date: 2018-08-27 14:29:48
 * @Last Modified by: wupeiwen javapeiwen2010@gmail.com
 * @Last Modified time: 2020-05-06 13:47:43
 * @Description: 液体填充
 */

<template>
  <div :id="id"></div>
</template>

<script>
import chartMix from '@/utils/chart.js'
import { percentFormat, floatIntFormat } from '@/utils/index'

export default {
  name: 'g2-liquidfill',
  mixins: [chartMix],
  props: {
    // 数据
    data: {
      type: Array,
      default: () => {
        return [{ name: '中国', value: 0.3 }]
      }
    },
    // 坐标轴名称
    axisName: {
      type: Object,
      default: () => {
        return {
          name: 'name',
          value: 'value'
        }
      }
    },
    // 最大值
    maxValue: {
      type: Number,
      default: 1
    },
    // value 数据是否是百分数（整数和百分数）
    isPercent: {
      type: Boolean,
      default: true
    },
    useTooltip: {
      type: Boolean,
      default: true
    },
    // Canvas 内边距
    padding: {
      type: Array,
      default: function () {
        return ['auto', 'auto']
      }
    },
    // 颜色配置
    color: {
      type: Object,
      default: function () {
        return {
          labelColor: 'rgba(0,0,0,0.65)',
          backgroundColor: '#1890FF'
        }
      }
    }
  },
  methods: {
    setChartConfig: function (data) {
      // 设置数据的显示别名以及最大值、最小值
      let _this = this
      let scaleConfig = (function () {
        let obj = {}
        for (const key in _this.axisName) {
          if (_this.axisName.hasOwnProperty(key)) {
            obj[key] = {}
            obj[key]['alias'] = _this.axisName[key]
            if (key === 'value') {
              obj[key]['max'] = _this.maxValue
              obj[key]['min'] = 0
            }
          }
        }
        return obj
      }())
      // 为 chart 装载数据
      this.chart.source(data, scaleConfig)

      // 隐藏图例
      this.chart.legend(false)

      // 隐藏坐标轴
      this.chart.axis(false)

      // 配置液体流动的边框颜色等
      this.chart.interval().position('name*value').color('name', this.color.backgroundColor)
        .shape('liquid-fill-gauge').style({
          lineWidth: 2,
          opacity: 0.65
        })

      // 是否使用tooltip
      if (this.useTooltip) {
        // 配置tooltip
        this.chart.tooltip(true)
      } else {
        this.chart.tooltip(false)
      }

      // 添加文本辅助元素
      const guideContent = this.isPercent ? percentFormat(this.data[0].value) : floatIntFormat(this.data[0].value)
      this.chart.guide().text({
        // 文本辅助元素位置
        top: true,
        position: {
          name: this.data[0].name,
          value: this.maxValue / 2
        },
        // 文本辅助元素内容
        content: guideContent,
        // 文本辅助元素样式
        style: {
          fill: this.color.labelColor,
          fontSize: this.height / 8,
          textAlign: 'center'
        }
      })
    }
  }
}
</script>
