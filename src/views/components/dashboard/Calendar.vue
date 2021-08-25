<template>
  <div class="text-center section">
    <v-calendar
      class="custom-calendar max-w-full"
      :masks="masks"
      :attributes="attributes"
      disable-page-swipe
      is-expanded
       :max-date="maxDate"
    >
      <template v-slot:day-content="{ day, attributes }">
        <div class="flex flex-col h-full z-10 overflow-hidden">
          <span class="day-label text-sm text-gray-900">{{ day.day }}</span>
          <div class="flex-grow overflow-y-auto overflow-x-auto">
            <p
              v-for="attr in attributes"
              :key="attr.key"
              class="text-xs leading-tight rounded-sm p-1 mt-0 mb-1"
              :class="attr.customData.class"
            >
              {{ attr.customData.title }}
            </p>
          </div>
        </div>
      </template>
    </v-calendar>
  </div>
</template>

<script>
const axios = require("axios");

export default {
 name: "Calendar",
  data() {
    return {
      masks: {
        weekdays: 'WWW',
      },
      attributes: [],
      maxDate: new Date(2020, 6, 14),
    };
  },
  mounted(){
       axios
      .get('https://swdapi.ddns.net:8090/data/ttntest')
      .then(response => {
        var data  = response.data;
        const groups = data.reduce((groups, game) => {
        const date = game.timestamp.split('T')[0];
        if (!groups[date]) {
            groups[date] = [];
        }
        groups[date].push(game);
        return groups;
        }, {});

        const groupArrays = Object.keys(groups).map((date) => {
        return {
            date,
            count: groups[date].length
        };
        });

        for(var i = 0; i < groupArrays.length; i++){
            var Year = parseInt(groupArrays[i]["date"].split("-")[0]);
            var Month = parseInt(groupArrays[i]["date"].split("-")[1]) - 1;
            var _Date = parseInt(groupArrays[i]["date"].split("-")[2]);
            this.attributes.push(
                  {
                    key: i + 1,
                    customData: {
                        title: "จำนวนข้อมูล " + groupArrays[i]["count"],
                        class: 'bg-red-600 text-white',
                    },
                    dates: new Date(Year, Month, _Date),
                    }
            );
        }
    });

  }
};
</script>

<style lang="postcss" scoped>
::-webkit-scrollbar {
  width: 0px;
}
::-webkit-scrollbar-track {
  display: none;
}
/deep/ .custom-calendar.vc-container {
  --day-border: 1px solid #b8c2cc;
  --day-border-highlight: 1px solid #b8c2cc;
  --day-width: 90px;
  --day-height: 90px;
  --weekday-bg: #f8fafc;
  --weekday-border: 1px solid #eaeaea;
  border-radius: 0;
  width: 100%;
  & .vc-header {
    background-color: #f1f5f8;
    padding: 10px 0;
  }
  & .vc-weeks {
    padding: 0;
  }
  & .vc-weekday {
    background-color: var(--weekday-bg);
    border-bottom: var(--weekday-border);
    border-top: var(--weekday-border);
    padding: 5px 0;
  }
  & .vc-day {
    padding: 0 5px 3px 5px;
    text-align: left;
    height: var(--day-height);
    min-width: var(--day-width);
    background-color: white;
    &.weekday-1,
    &.weekday-7 {
      background-color: #eff8ff;
    }
    &:not(.on-bottom) {
      border-bottom: var(--day-border);
      &.weekday-1 {
        border-bottom: var(--day-border-highlight);
      }
    }
    &:not(.on-right) {
      border-right: var(--day-border);
    }
  }
  & .vc-day-dots {
    margin-bottom: 5px;
  }
}

.text-white {
        color: #4cae4c !important;
}
</style>