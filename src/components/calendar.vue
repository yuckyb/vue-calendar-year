<template>
  <div class="calender-box">
    <div class="month-box" v-for="(item, index) in dateList" :key="index">
      <div class="date-year-month" style="text-align: center;font-size:35rpx;">{{item.year}}年{{item.month}}月</div>
      <div class="weeks">
        <div class="week" :class="(index==0 || index==6)?'week-active ':''" v-for="(item, index) in weekStr" :key="index">
          <span>{{item}}</span>
        </div>
      </div>
      <div class="days">
        <div class="day" :class="item.week?'week-active':''" v-for="(item, index) in item.days" :key="index" @click="onPressDate(item)">
          <div :class="item.clickactive?'day-active':''">
            <span :style="item.day<1?'display:none':''">{{item.day}}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
var Moment = require("../utils/moment.js");
var DATE_LIST = [];
var DATE_YEAR = new Date().getFullYear();
var DATE_MONTH = new Date().getMonth() + 1;
var DATE_DAY = new Date().getDate();
export default {
  name: 'calendar',
  data () {
    return {
    maxMonth: 12, //最多渲染月数
    dateList: [],
    weekStr: ['日', '一', '二', '三', '四', '五', '六'],
     sFtv: [
      {
        month:1,
        day:1,
        name:"元旦"
      },
      {
        month: 2,
        day: 14,
        name: "情人节"
      },
      {
        month: 3,
        day: 8,
        name: "妇女节"
      },
      {
        month: 3,
        day: 12,
        name: "植树节"
      },
      {
        month: 3,
        day: 15,
        name: "消费者权益日"
      },
      {
        month: 4,
        day: 1,
        name: "愚人节"
      },
      {
        month: 5,
        day: 1,
        name: "劳动节"
      },
      {
        month: 5,
        day: 4,
        name: "青年节"
      },
      {
        month: 5,
        day: 12,
        name: "护士节"
      },
      {
        month: 6,
        day: 1,
        name: "儿童节"
      },
      {
        month: 7,
        day: 1,
        name: "建党节"
      },
      {
        month: 8,
        day: 1,
        name: "建军节"
      },
      {
        month: 9,
        day: 10,
        name: "教师节"
      },
      {
        month: 9,
        day: 28,
        name: "孔子诞辰"
      },
      {
        month: 10,
        day: 1,
        name: "国庆节"
      },
      {
        month: 10,
        day: 6,
        name: "老人节"
      },
      {
        month: 10,
        day: 24,
        name: "联合国日"
      },
      {
        month: 12,
        day: 24,
        name: "平安夜"
      },
      {
        month: 12,
        day: 25,
        name: "圣诞节"
      }
    ]
    }
  },
  mounted(){
    this.createDateListData();
  },
  methods:{
    
  createDateListData () {
    var dateList = [];
    var now = new Date();
    /*
      设置日期为 年-月-01,否则可能会出现跨月的问题
      比如：2017-01-31为now ,月份直接+1（now.setMonth(now.getMonth()+1)），则会直接跳到跳到2017-03-03月份.
        原因是由于2月份没有31号，顺推下去变成了了03-03
    */
    now = new Date(now.getFullYear(), 0, 1);
    for (var i = 0; i < this.maxMonth; i++) {
      var momentDate = Moment(now).add(this.maxMonth - (this.maxMonth - i), 'month').date;
      var year = momentDate.getFullYear();
      var month = momentDate.getMonth() + 1;
      var days = [];
      var totalDay = this.getTotalDayByMonth(year, month);
      var week = this.getWeek(year, month, 1);
       var week_day=false;
      //-week是为了使当月第一天的日期可以正确的显示到对应的周几位置上，比如星期三(week = 2)，
      //则当月的1号是从列的第三个位置开始渲染的，前面会占用-2，-1，0的位置,从1开正常渲染
      for (var j = -week + 1; j <= totalDay; j++) {
        var week_active=false;
        if(j>0){
           var week_day=this.getWeeks(year,month,j);
        if(week_day==6 || week_day==5){
            week_active=true;
        } 
        }
        days.push({ year:year,month:month,day: j,  date: year + '-' + month+'-'+j,week:week_active ,clickactive:false})
      }
      var dateItem = {
        id: year + '-' + month,
        year: year,
        month: month,
        days: days
      }
      dateList.push(dateItem);
    }
    // var sFtv = this.sFtv;
    // for (let i = 0; i < dateList.length; i++){//加入公历节日
    //    for(let k = 0; k < sFtv.length; k++){
    //      if (dateList[i].month == sFtv[k].month){
    //        let days = dateList[i].days;
    //        for (let j = 0; j < days.length; j++){
    //          if (days[j].day == sFtv[k].day){
    //            days[j].daytext = sFtv[k].name
    //          }
    //        }
    //      }
    //    }
    // }
    this.dateList=dateList;
    DATE_LIST = dateList;
  },

  /*
	 * 获取月的总天数
	 */
  getTotalDayByMonth (year, month) {
    month = parseInt(month, 10);
    var d = new Date(year, month, 0);
    return d.getDate();
  },
  /**
   * 获取周末
   */
  getWeeks(year, month, day){
    var d = new Date(year, month-1, day-1);
    return d.getDay();
  },

	/*
	 * 获取月的第一天是星期几
	 */
  getWeek (year, month, day) {
    var d = new Date(year, month - 1, day);
    return d.getDay();
  },
  /**
   * 点击日期事件
   */
  onPressDate (e) {
    console.log(e,'点击日期')
    var { year, month, day } = e;
    console.log(year,month,day)
    //渲染点击样式
    for (var i = 0; i < this.dateList.length; i++) {
      var dateItem = this.dateList[i];
      var id = dateItem.id;
      if (id === year + '-' + month) {
        var days = dateItem.days;
        for (var j = 0; j < days.length; j++) {
          var tempDay = days[j].day;
          if (tempDay == day) {
            days[j].clickactive = !days[j].clickactive;
          }
        }
      }
    }
    console.log(this.dateList,'dateList')
  },











  
  }
}
</script>

<style scoped>
.calender-box{
  display: flex;
  flex-direction: row;
  flex-wrap:wrap;
  justify-content: center;
  font-size: 14px;
}
.date-year-month{
  padding: 15px 0;
}
.month-box{
  width: 30%;
  border: 1px solid  #ea6151;
  margin-right: 10px;
}
.month{
  line-height: 30px;
}
.weeks{
  background: #f5f5f5;
  display: flex;
  align-items: center;
}
.weeks .week{
  width: 14.2%;
  padding: 10px 0;
}
.days{
  display: flex;
  align-items: center;
   flex-wrap:wrap;
}
.days .day{
  width: 14.2%;
  margin-top:1px;
}
.days .day span{
  padding: 15px 0;
  display: inline-block;
}
.week-active{
 background-color: #ea6151;
  color: #fff;
}
.day-active{
  background: #5e7a88;
  width: 100%;
  color: #fff;
}
</style>
