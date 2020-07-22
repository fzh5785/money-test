<template>
  <layout>
    <Tabs class-prefix="type" :data-source="recordTypeList"
          :value.sync="type"/>

    <div class="chart-wrapper" ref="chartWrapper">
      <Chart class="chart" :options="chartOptions"/>
    </div>

    <ol v-if="groupedList.length>0" class="group-wrapper">
      <li v-for="(group,index) in groupedList" :key="index">
        <h3 class="title">{{beautify(group.title)}} <span>￥{{group.total}}</span></h3>
        <ol>
          <li v-for="item in group.items" :key="item.id"
              class="record">
            <span>{{tagString(item.tags)}}</span>
            <span class="notes">{{item.notes}}</span>
            <span>￥{{item.amount}} </span>
          </li>
        </ol>
      </li>
    </ol>
    <div v-else class="noResult">
      目前没有相关记录
    </div>
  </layout>
</template>

<script lang="ts">
  import Vue from 'vue';
  import Tabs from '@/components/Tabs.vue';
  import {Component} from 'vue-property-decorator';
  import recordTypeList from '@/constants/recordTypeList';
  import dayjs from 'dayjs';
  import clone from '@/lib/clone';
  import Chart from '@/components/Chart.vue';
  import _ from 'lodash';

  @Component({
    components: {Chart, Tabs},
  })
  export default class Statistics extends Vue {
    mounted() {
      const div = this.$refs.chartWrapper as HTMLDivElement;
      div.scrollLeft = div.scrollWidth;
    }

    get keyValueList() {
      const today = new Date();
      const array = [];
      for (let i = 0; i <= 29; i++) {
        const dateString = dayjs(today).subtract(i, 'day').format('YYYY-MM-DD');
        const found = _.find(this.groupedList, {title: dateString});
        array.push({
          key: dateString, value: found ? found.total : 0
        });
      }
      array.sort((a, b) => {
        if (a.key > b.key) {
          return 1;
        } else if (a.key === b.key) {
          return 0;
        } else {
          return -1;
        }
      });
      return array;
    }

    get chartOptions() {

      const keys = this.keyValueList.map(item => item.key);
      const values = this.keyValueList.map(item => item.value);
      return {
        title: {
          text :'统计图',
          right : 145,
          top:15,
          textStyle: {
            //fontWeight : 'normal',
            fontSize: 14
          }
        },
        grid: {
          left: 0,
          right: 0,
          top: 60,
          bottom: 50
        },
        xAxis: {
          type: 'category',
          data: keys,
          axisTick: {
            alignWithLabel: true
          },
          axisLine: {
            lineStyle: {
              color: 'rgb(80, 131, 255)'
            }
          },
          axisLabel: {
            color: '#333',
            formatter: function (value: string) {
              return value.substr(5);
            }
          }
        },
        yAxis: {
          type: 'value',
          axisLine: {
            show :false
          },
          splitLine: {
            show : true,
          },
        },
        series: [{
          symbol: 'circle',
          itemStyle: {
            color: 'rgb(80, 131, 255)'
          },
          data: values,
          symbolSize: 12,
          type: 'line'
        }],
        tooltip: {
          show: true,
          formatter: '{c}',
          position: 'top'
        }
      };
    }

    beautify(string: string) {
      const now = dayjs();
      const day = dayjs(string);
      if (day.isSame(now, 'day')) {
        return '今天';
      } else if (day.isSame(now.subtract(1, 'day'), 'day')) {
        return '昨天';
      } else if (day.isSame(now.subtract(2, 'day'), 'day')) {
        return '前天';
      } else if (day.isSame(now, 'year')) {
        return day.format('M月D日');
      } else {
        return day.format('YYYY年M月D日');
      }
    }

    tagString(tags: Tag[]) {
      return tags.length === 0 ? '无' : tags.map(t => t.name).join('，');
    }

    get recordList() {
      return (this.$store.state as RootState).recordList;
    }

    get groupedList() {
      const {recordList} = this;

      const newList = clone(recordList).filter(r => r.type === this.type).sort((a, b) => dayjs(b.createdAt).valueOf() - dayjs(a.createdAt).valueOf());
      if (newList.length === 0) {return [];}
      type Result = { title: string; total?: number; items: RecordItem[] }[]
      const result: Result = [{title: dayjs(newList[0].createdAt).format('YYYY-MM-DD'), items: [newList[0]]}];
      for (let i = 1; i < newList.length; i++) {
        const current = newList[i];
        const last = result[result.length - 1];
        if (dayjs(last.title).isSame(dayjs(current.createdAt), 'day')) {
          last.items.push(current);
        } else {
          result.push({title: dayjs(current.createdAt).format('YYYY-MM-DD'), items: [current]});
        }
      }
      result.map(group => {
        group.total = group.items.reduce((sum, item) => sum + item.amount, 0);
      });
      return result;

    }

    beforeCreate() {
      this.$store.commit('fetchRecords');
    }

    type = '-';
    recordTypeList = recordTypeList;
  }
</script>

<style lang="scss" scoped>
  .group-wrapper {
    width: 90%;
    background: white;
    margin: 30px auto 16px;
    box-shadow: 0 2px 2px rgba(10,16,20,.14),0 0 2px rgba(10,16,20,.12);
  }
  .chart {
    width: 430%;

    &-wrapper {
      overflow: auto;
      width: 90%;
      background: white;
      border-radius: 2%;
      margin: 30px auto 0;
      box-shadow: 0 2px 2px rgba(10,16,20,.14),0 0 2px rgba(10,16,20,.12);

      &::-webkit-scrollbar {
        display: none;
      }
    }
  }

  .noResult {
    padding: 16px;
    text-align: center;
  }

  %item {
    padding: 8px 16px;
    line-height: 24px;
    display: flex;
    justify-content: space-between;
    align-content: center;
  }

  .title {
    @extend %item;
    background: #f5f5f5;

  }

  .record {

    @extend %item
  }

  .notes {
    margin-right: auto;
    margin-left: 16px;
    color: #999;
  }

  ::v-deep {
    .type-tabs-item {
      background: white;
      &.selected {
        background: rgb(80, 131, 255);
        color: white;

        &::after {
          display: none;
        }
      }
    }

    .interval-tabs-item {
      height: 36px;
      font-size: 18px;
    }
  }
</style>