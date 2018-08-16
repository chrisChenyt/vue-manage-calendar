<template>
  <div class="__vev_calendar-wrapper">
    <cal-panel
      :events="events"
      :calendar="calendarOptions"
      :selectedDay='selectedDayEvents.date'
      @cur-day-changed="handleChangeCurDay"
      @all-month="allMonth"
      @open-picker="openPicker"
      :date="date"
      @month-changed="handleMonthChanged">
    </cal-panel>
  </div>
</template>
<script>
/* eslint-disable*/
import { dateTimeFormatter, isEqualDateStr} from './tools.js'
import calPanel from './components/cal-panel.vue'

const inBrowser = typeof window !== 'undefined'
export default {
  name: 'vue-manage-calendar',
  components: {
    'cal-panel': calPanel
  },
  data () {
    return {
      selectedDayEvents: {
        date: 'all',
        events: this.events || []  //default show all event
      }
    }
  },
  props: {
    title: String,
    events: {
      type: Array,
      required: true,
      default: [],
      validator (events) {
        let validate = true
        events.forEach((event, index) => {
          if (!event.date) {
            console.error('vue-manage-calendar-Error:' + 'Prop events Wrong at index ' + index)
            validate = false
          }
        })
        return validate
      }
    },
    date:{
      type: String,
      required: false
    }
  },
  computed: {
    calendarOptions () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA
      } else {
        return {
          options: {
            locale: 'en', //zh
            color: '#FFA746'
          },
          params: {
              curYear: dateObj.getFullYear(),
              curMonth: dateObj.getMonth(),
              curDate: dateObj.getDate(),
              curEventsDate: 'all'
          }
        }
      }
    },
    calendarParams () {
      let dateObj = new Date()
      if (inBrowser) {
          return window.VueCalendarBarEventBus.CALENDAR_EVENTS_DATA.params
      } else {
        return {
          curYear: dateObj.getFullYear(),
          curMonth: dateObj.getMonth(),
          curDate: dateObj.getDate(),
          curEventsDate: 'all'
        }
      }
    }
  },
  created () {
    if (this.calendarParams.curEventsDate !== 'all') {
      this.handleChangeCurDay(this.calendarParams.curEventsDate)
    }
  },
  methods: {
    handleChangeCurDay (date) {
      let events = this.events.filter(function(event) {
        return isEqualDateStr(event.date, date)
      })
      if (events.length > 0) {
        this.selectedDayEvents = {
          date: date,
          events: events
        }
      }
      if(events != ''){
        this.$emit('day-changed', dateTimeFormatter(date,'yyyy-MM-dd'))
      }
    },
    handleMonthChanged (yearMonth) {
      this.$emit('month-changed', yearMonth)
    },
    allMonth (yearMonth) {
      this.selectedDayEvents = {
        date: 'all',
        events: this.events || []  //default show all event
      }
      this.$emit('all-month', yearMonth)
    },
    openPicker () {
      this.$emit('open-picker')
    }
  },
  watch: {
    calendarParams () {
      if (this.calendarParams.curEventsDate !== 'all') {
        let events = this.events.filter(event => {
          return isEqualDateStr(event.date, this.calendarParams.curEventsDate)
        })
        this.selectedDayEvents = {
          date: this.calendarParams.curEventsDate,
          events
        }
      } else {
        this.selectedDayEvents = {
          date: 'all',
          events: this.events
        }
      }
    },
    events () {
      this.selectedDayEvents = {
        date: 'all',
        events: this.events || []
      }
    }
  }
}
</script>
<style lang="less">
.__vev_calendar-wrapper .cal-wrapper {
  width:100%;
  padding: 0 15px;
}
.__vev_calendar-wrapper {
	position:relative;
	overflow:hidden;
	width:100%;
}
.__vev_calendar-wrapper * {
	box-sizing:border-box
}
.__vev_calendar-wrapper::-webkit-scrollbar {
	width:8px;
	height:8px;
}
.__vev_calendar-wrapper::-webkit-scrollbar-track {
	box-shadow:inset 0 0 2px rgba(0,0,0,.2);
	border-radius:5px;
}
.__vev_calendar-wrapper::-webkit-scrollbar-thumb {
	border-radius:5px;
	background:rgba(0,0,0,.2);
}
.__vev_calendar-wrapper .cal-wrapper .cal-header {
	position:relative;
	width:100%;
  background-color:#fff;
  height: 32px;
  padding-bottom: 6.5px;
  border-bottom: 1px solid #E5E5E5;
  margin: 30px 0 15px;
}
.__vev_calendar-wrapper .cal-wrapper .cal-header .title {
  font-size: 14px;
  color: #5F5550;
  float: left;
}
// .__vev_calendar-wrapper .cal-wrapper .cal-header .title .triangle_border_down{
//   width:0;
//   height:0;
//   border-width: 4px 3px 0;
//   border-style: solid;
//   float: right;
//   margin: 8px 0 0 3px;
//   border-color:#5F5550 transparent transparent;/*灰 透明 透明 */
// }
.__vev_calendar-wrapper .cal-wrapper .cal-header .month-pandect{
  font-size: 12px;
  color: #5F5550;
  border: 1px solid #5F5550;
  border-radius: 6px;
  padding: 2px 8px;
  float: right;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body {
  width:100%;
  padding: 0 27px;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .weeks {
	width:100%;
	overflow:hidden;
  text-align:center;
  font-family: PingFangSC-Regular;
  font-size: 14px;
  color: #A69B96;
  margin-bottom: 16px;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .weeks .item {
	float:left;
	width:14.285%;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates {
	width:100%;
	overflow:hidden;
	text-align:center;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item {
	position:relative;
	float:left;
	display:block;
	width:14.285%;
	-webkit-tap-highlight-color:rgba(0,0,0,0);
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item .date-num {
	font-size: 14px;
  color: #5F5550;
	position:relative;
	z-index:3;
  padding-bottom: 15px;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item .noCurentMonth {
  color: #CCCCCC;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item.selected-day .is-invest {
	background-color:#FFA746;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item .is-invest {
	background-color:#fff;
	border-radius:50%;
	width:36px;
	height:36px;
	position:absolute;
	left:50%;
	top:50%;
	z-index:1;
	margin-left:-18px;
  margin-top:-19px;
  line-height: 55px;
  overflow: hidden;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item i{
  font-size: 11px;
  -webkit-transform:scale(0.8);
  font-style: normal;
  display: block;
	width:37px;
  height:36px;
  text-align: center;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item.selected-day .is-return {
	background-color:#FF4657;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item .is-return {
	background-color:#fff;
	border-radius:50%;
	width:36px;
	height:36px;
	position:absolute;
	left:50%;
	top:50%;
	z-index:1;
	margin-left:-18px;
  margin-top:-19px;
  line-height: 55px;
  overflow: hidden;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item.selected-day .is-double {
	background-color:#508DEA;
}
.__vev_calendar-wrapper .cal-wrapper .cal-body .dates .item .is-double {
	background-color:#fff;
	border-radius:50%;
	width:36px;
	height:36px;
	position:absolute;
	left:50%;
	top:50%;
	z-index:1;
	margin-left:-18px;
  margin-top:-19px;
  line-height: 55px;
  overflow: hidden;
  color: #508DEA;
}

.icon-xiaotuziCduan_{font-size:30px !important;color: #E5E5E5;}
.icon-xiaotuziCduan_1{font-size:30px !important;color: #E5E5E5;}
.__vev_calendar-wrapper .icon {
	position:absolute;
  margin-top: 90px;
}
.__vev_calendar-wrapper .arrow-left.icon {
  left: 15px;
}
.__vev_calendar-wrapper .arrow-right.icon {
	right:15px;
}
.__vev_calendar-wrapper h3,.__vev_calendar-wrapper p {
	margin:0;
	padding:0;
}

</style>
