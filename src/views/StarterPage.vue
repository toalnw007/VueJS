<template>
 <div>
 <vs-row vs-justify="center">
    <vs-col vs-lg="12">
     <button
      v-for="tab in tabs"
      :key="tab"
      @click="selected = tab;"
      :class="['tab-btn', { active: selected === tab }]"
    >
      {{ tab }}
    </button>

     <component :is="selected"></component>
    </vs-col>
  </vs-row>
</div>  
</template>

<script>
import List from './components/dashboard/List.vue';
import Calendar from './components/dashboard/Calendar.vue';
import Chart from './components/dashboard/LineChart.vue';

export default {
	name: 'StarterPage',
  components: {
    List,
    Calendar,
    Chart
  },
  data: () => ({
      theme: {
        nav: "#000080",
        navItem: "#ffffff",
        navActiveItem: "#fff",
        slider: "#CE93D8",
        arrow: "#f3f3f3",
      },
       tabs: ["List", "Calendar", "Chart"],
      selected: "List"
    }),
}
</script>
<style>
.tab-btn {
  padding: 6px 10px;
  background: #ffffff;
  cursor: pointer;
  margin-bottom: 1rem;
  border: 2px solid #cccccc;
  outline: none;
}

.active {
  border-bottom: 3px solid green;
  background: #fcfcfc;
}

.tab {
  border: 1px solid #ccc;
  padding: 10px;
}
</style>