<template>
  <div id="app">
    <vue-manage-calendar
      :events="demoEvents"
      :date="date"
      @day-changed="handleDayChanged"
      @month-changed="handleMonthChanged"
      @open-picker="openPicker"
      @all-month="handleAllMonth"
    ></vue-manage-calendar>
    <picker class="base-picker" v-model="visible" :data-items="items" @change="onBaseValuesChange"></picker>
  </div>
</template>

<script>
/* eslint-disable*/
import picker from 'vue-3d-picker'
export default {
  name: 'app',
  data () {
    return {
      demoEvents: [{
        date: '2018/08/15',
        type: '1'
      },{
        date: '2018/08/16',
        type: '2'
      },{
        date: '2018/09/12',
        type: '1'
      },{
        date: '2018/10/17',
        type: '3'
      },{
        date: '2018/08/20',
        type: '3'
      }],
      visible: false,
      items: [
        {
          values: ['2015', '2016', '2017', '2018', '2019', '2020'],
        }, {
          values: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
        }
      ],
      date: ''
    }
  },
  components: {
    picker
  },
  methods: {
    handleDayChanged (data) {
      console.log('day-change',data)
    },
    handleMonthChanged (data) {
      console.log('month-change',data)
    },
    handleAllMonth (data) {
      console.log('all-change',data)
    },
    onBaseValuesChange (year, month) {
      console.log(year, month)
      this.$EventCalendar.toDate(year+'/'+month.replace('月',''))
    },
    openPicker () {
      this.visible = true
    }
  }
}
</script>

<style>
</style>
