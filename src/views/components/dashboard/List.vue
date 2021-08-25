<template>
<div class="table-responsive">    
  <DxDataGrid
    :columns="columns"
     key-expr="id"
    :show-borders="true"
    :data-source="dataSource"
  >
      <DxSummary>
        <DxTotalItem
          column="id"
          summary-type="count"
        />
      </DxSummary>
  </DxDataGrid>
</div>
</template>
<script>
const axios = require("axios");
import {
  DxDataGrid,
  DxSummary,
  DxTotalItem
} from 'devextreme-vue/data-grid';

import moment from 'moment';


export default {
    name: "List",
    components: {
    DxDataGrid,
    DxSummary,
    DxTotalItem
  },
   data() {
    return {
    dataSource: null,
    columns: ['id', 'data', 'data2', 'timestamp']
    };
  },
  mounted() {
    axios
      .get('https://swdapi.ddns.net:8090/data/ttntest')
      .then(response => {
        var dataList = response.data;
          for(var i=0; i<dataList.length; i++){
            dataList[i]["timestamp"] = moment(dataList[i]["timestamp"]).tz("Asia/Bangkok").format("YYYY-MM-DD HH:mm:ss");
          }
          this.dataSource = dataList;
        })
  }
}
</script>