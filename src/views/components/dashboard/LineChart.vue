<template>
  <div id="chart-demo">
    <DxChart
      id="chart"
      :data-source="weatherData"
    >
      <DxCommonSeriesSettings argument-field="timestamp"/>
      <DxSeries
        axis="data2"
        color="#03a9f4"
        type="bar"
        value-field="data2"
        name="Data2"
      />
      <DxSeries
        axis="data"
        color="#e91e63"
        value-field="data"
        name="Data1"
      >
        <DxPoint :size="7"/>
      </DxSeries>

      <DxValueAxis name="data">
        <DxTitle text="Data1">
          <DxFont color="#e91e63"/>
        </DxTitle>
        <DxLabel>
          <DxFont color="#e91e63"/>
        </DxLabel>
      </DxValueAxis>
      <DxValueAxis
        name="data2"
        position="right"
      >
        <DxTitle text="Data2">
          <DxFont color="#03a9f4"/>
        </DxTitle>
        <DxLabel>
          <DxFont color="#03a9f4"/>
        </DxLabel>
      </DxValueAxis>

      <DxLegend :visible="true"/>
      <DxTooltip
        :enabled="true"
      />
      <DxTitle text="Average Data"/>
    </DxChart>
  </div>
</template>
<script>

const axios = require("axios");

import DxChart, {
  DxCommonSeriesSettings,
  DxSeries,
  DxPoint,
  DxValueAxis,
  DxTitle,
  DxFont,
  DxLegend,
  DxLabel,
  DxTooltip
} from 'devextreme-vue/chart';

export default {
   name: "LineChart",
    components: {
    DxChart,
    DxCommonSeriesSettings,
    DxSeries,
    DxPoint,
    DxValueAxis,
    DxTitle,
    DxFont,
    DxLegend,
    DxLabel,
    DxTooltip
  },
  data() {
    return {
      weatherData: null,
      useAggregation: true,
      currentFunction: "avg",
      currentInterval: "week"
    };
  },
  mounted() {
     axios
      .get('https://swdapi.ddns.net:8090/data/ttntest')
      .then(response => {
        var data = response.data;
        for(var i = 0; i < data.length; i++){
          data[i]["timestamp"] = data[i]["timestamp"].split("T")[0];
        }
        this.weatherData = data;
        })
  },
  methods: {
          getAverages(array, groupKeys, averageKeys) {
          var groups = {},
              result = [];

          array.forEach(o => {
              var key = groupKeys.map(k => o[k]).join('|'),
                  group = groups[key];

              if (!group) {
                  groups[key] = { count: 0, payload: {} };
                  group = groups[key];
                  averageKeys.forEach(k => group[k] = 0);
                  groupKeys.forEach(k => group.payload[k] = o[k]);
                  result.push(group.payload);
              }
              groups[key].count++;
              averageKeys.forEach(k => group.payload[k] = (group[k] += o[k]) / group.count);
          })
          return result;
      },
      calculateRangeArea(aggregationInfo) {
      if(!aggregationInfo.data.length) {
        return;
      }

      let temp = aggregationInfo.data.map(item => item.temp);
      return {
        date: new Date((aggregationInfo.intervalStart.valueOf() + aggregationInfo.intervalEnd.valueOf()) / 2),
        maxTemp: Math.max.apply(null, temp),
        minTemp: Math.min.apply(null, temp)
      };
    },

  }
};
</script>
<style>
#chart-demo {
    height: 700px;
}

#chart {
    height: 500px;
    margin: 0 0 15px;
}
</style>