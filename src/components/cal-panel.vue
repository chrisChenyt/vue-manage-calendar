<template>
  <div class="cal-wrapper">
    <div class="cal-header">
      <div class="title" @click="openPicker">
        <span>{{curYearMonth}}</span>
        <img :src="openUrl">
        <!-- <span class="triangle_border_down"></span> -->
      </div>
      <div class="month-pandect" @click="allMonth()">月度总览</div>
    </div>
    <div class="cal-body">
      <div class="l" @click="preMonth">
        <img class="arrow-left icon" src="../images/left@2x.png">
      </div>
      <div class="r" @click="nextMonth">
        <img class="arrow-right icon" src="../images/right@2x.png">
      </div>
      <div class="weeks">
        <span
          v-for="(dayName, dayIndex) in i18n[calendar.options.locale].dayNames"
          class="item"
          :key="dayIndex"
          >
          {{i18n[calendar.options.locale].dayNames[(dayIndex + calendar.options.weekStartOn) % 7]}}
        </span>
      </div>
      <div class="dates" >
        <div v-for="date in dayList" 
          class="item"
          :class="{event: date.type}"
          :key="date.date"
          >
          <p class="date-num"
            @click="handleChangeCurday(date)"
            :style="{color: (date.date == selectedDay) ? '#fff' : date.color}">
            {{date.status ? date.date.split('/')[2] : '&nbsp;'}}</p>
            <!-- {{date.date.split('/')[2]}}</p> -->
            <!-- :class="{noCurentMonth: date.status==0}" -->
          <!-- 投资 -->
          <span v-if="date.type == '1'" class="is-invest"
            :style="{backgroundColor: (date.date == selectedDay) ? date.color : 'inherit',color: (date.date == selectedDay) ? '#fff' : date.color}"><i>投资</i></span>
          <!-- 回款 -->
          <span v-if="date.type == '2'" class="is-return"
            :style="{backgroundColor: (date.date == selectedDay) ? date.color : 'inherit',color: (date.date == selectedDay) ? '#fff' : date.color}"><i>回款</i></span>
          <!-- 投/回 -->
          <span v-if="date.type == '3'" class="is-double"
            :style="{backgroundColor: (date.date == selectedDay) ? date.color : 'inherit',color: (date.date == selectedDay) ? '#fff' : date.color}"><i>投/回</i></span>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable*/
import i18n from '../i18n.js'
import { dateTimeFormatter, isEqualDateStr} from '../tools.js'
const inBrowser = typeof window !== 'undefined'
export default {
  name: 'cal-panel',
  data () {
    return {
      i18n,
      openUrl:require('../images/open@2x.png')
    }
  },
  props: {
    events: {
      type: Array,
      required: true
    },
    calendar: {
      type: Object,
      required: true
    },
    selectedDay: {
      type: String,
      required: false
    },
    date:{
      type: String,
      required: false
    }
  },
  computed: {
    dayList () {
      let firstDay = new Date(this.calendar.params.curYear, this.calendar.params.curMonth, 1)
      let dayOfWeek = firstDay.getDay()
      // 根据当前日期计算偏移量 // Calculate the offset based on the current date
      if (this.calendar.options.weekStartOn > dayOfWeek) {
        dayOfWeek = dayOfWeek - this.calendar.options.weekStartOn + 7
      } else {
        dayOfWeek = dayOfWeek - this.calendar.options.weekStartOn
      }

      let startDate = new Date(firstDay)
      startDate.setDate(firstDay.getDate() - dayOfWeek)

      let item, status, tempArr = [], tempItem
      for (let i = 0 ; i < 42 ; i++) {
          item = new Date(startDate);
          item.setDate(startDate.getDate() + i);

          if (this.calendar.params.curMonth === item.getMonth()) {
            status = 1
          } else {
            status = 0
          }
          
          tempItem = {
            date: `${item.getFullYear()}/${item.getMonth()+1}/${item.getDate()}`,
            status: status
          }
          this.events.forEach((event) => {
            if (isEqualDateStr(event.date, tempItem.date)) {
              tempItem.type = event.type
              if(event.type == '1'){
                tempItem.color = '#FFA746'
              }else if(event.type == '2'){
                tempItem.color = '#FF4657'
              } if(event.type == '3'){
                tempItem.color = '#508DEA'
              }
            }
          })
          tempArr.push(tempItem)
      }
      return tempArr
    },
    curYearMonth () {
      let tempDate = Date.parse(new Date(`${this.calendar.params.curYear}/${this.calendar.params.curMonth+1}/01`))
      return dateTimeFormatter(tempDate, 'yyyy-MM')
    }
  },
  methods: {
    nextMonth () {
      this.$EventCalendar.nextMonth()
      this.$emit('month-changed', this.curYearMonth)
    },
    preMonth () {
      this.$EventCalendar.preMonth()
      this.$emit('month-changed', this.curYearMonth)
    },
    handleChangeCurday (date) {
      if (date.status) {
        this.$emit('cur-day-changed', date.date)
      }
    },
    allMonth () {
      this.$emit('all-month',this.curYearMonth)
    },
    openPicker () {
      this.$emit('open-picker')
    },
    toDate () {
      console.log(this.date)
      this.$EventCalendar.toDate(this.date)
    }
  }
}
</script>
