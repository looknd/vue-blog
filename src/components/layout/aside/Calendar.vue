<template>
  <div class="calendar-box">
    <!-- 年份 月份 -->
    <div class="months">
      <ul class="month-list">
        <li class="arrow prev" @click="pickPre(currentYear, currentMonth)">❮</li>
        <li class="year-month">
            <span class="choose-year">{{ currentYear }}年{{ currentMonth }}月{{ currentDay }}日</span>
            <span class="choose-time">{{ currentTime }}</span>
        </li>
        <li class="arrow next" @click="pickNext(currentYear, currentMonth)">❯</li>
      </ul>
    </div>
    <!-- 星期 -->
    <ul class="weekdays">
      <li>一</li>
      <li>二</li>
      <li>三</li>
      <li>四</li>
      <li>五</li>
      <li>六</li>
      <li>日</li>
    </ul>
    <!-- 日期 -->
    <ul class="days">
      <li v-for="day in days">
        <!--本月-->
        <span v-if="day.getMonth()+1 != currentMonth" class="other-month">{{ day.getDate() }}</span>
        <span v-else class="item" :class="{ 'active': day.getFullYear() == new Date().getFullYear() && day.getMonth() == new Date().getMonth() && day.getDate() == new Date().getDate() }">
          <!--today-->
          <router-link :to="'/date/' + formatDate(day.getFullYear(), day.getMonth() + 1, day.getDate())">{{ day.getDate() }}</router-link>
        </span>
      </li>
    </ul>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        currentDay: 1,
        currentMonth: 1,
        currentYear: 1970,
        currentWeek: 1,
        currentTime: '',
        days: []
      }
    },

    mounted() {
      this.initData(null)
      this.updateTime()
    },
    methods: {

      initData(cur) {
        let date;
        if (cur) {
          date = new Date(cur);
        } else {
          date = new Date();
        }
        this.currentDay = date.getDate()
        this.currentYear = date.getFullYear()
        this.currentMonth = date.getMonth() + 1
        this.currentWeek = date.getDay()
        if (this.currentWeek == 0) this.currentWeek = 7
        let str = this.formatDate(this.currentYear, this.currentMonth, this.currentDay);
        // console.log("today:" + str + "," + this.currentWeek);
        this.days.length = 0;
        // 今天是周日，放在第一行第7个位置，前面6个
        for (var i = this.currentWeek - 1; i >= 0; i--) {
          var d = new Date(str);
          d.setDate(d.getDate() - i);
          // console.log("y:" + d.getDate());
          this.days.push(d);
        }
        for (var i = 1; i <= 35 - this.currentWeek; i++) {
          var d = new Date(str);
          d.setDate(d.getDate() + i);
          this.days.push(d);
        }
      },

      pick(date) {
        alert(this.formatDate(date.getFullYear(), date.getMonth() + 1, date.getDate()))
      },

      pickPre(year, month) {
        //  setDate(0); 上月最后一天
        //  setDate(-1); 上月倒数第二天
        //  setDate(dx) 参数dx为 上月最后一天的前后dx天
        var d = new Date(this.formatDate(year, month, 1));
        d.setDate(0);
        this.initData(this.formatDate(d.getFullYear(), d.getMonth() + 1, 1));
      },

      pickNext(year, month) {
        var d = new Date(this.formatDate(year, month, 1));
        d.setDate(35);
        this.initData(this.formatDate(d.getFullYear(), d.getMonth() + 1, 1));
      },

      pickYear(year, month) {
        alert(year + "," + month);
      },

      // 返回 类似 2016-01-02 格式的字符串
      formatDate(year, month, day) {
        var y = year;
        var m = month;
        if (m < 10) m = "0" + m;
        var d = day;
        if (d < 10) d = "0" + d;
        return y + "-" + m + "-" + d
      },

      updateTime() {
        let _this = this
        const showLocale = function() {
          const objD = new Date()
          let hh = objD.getHours()
          if(hh < 10) hh = '0' + hh
          let mm = objD.getMinutes()
          if(mm < 10) mm = '0' + mm
          let ss = objD.getSeconds()
          if(ss < 10) ss = '0' + ss
          if (['59', '00'].includes(ss) || !_this.currentTime) _this.currentTime = `${hh} : ${mm}`
        }
        setInterval(showLocale, 1000)
      }
    }
  }
</script>

<style lang="scss" scoped>
  @import '../../../sass/variables';
  .calendar-box {

    > .months {
      margin-bottom: .5em;

      > .month-list {
        padding: 0;
        margin: 0;
        list-style: none;
        overflow: hidden;

        > li {
          float: left;
          height: 2em;
          line-height: 2em;
          text-align: center;

          &.year-month {
            width: 11.4em;
          }

          &.arrow {
            width: 2em;
            background-color: $module-hover-bg;
            cursor: pointer;

            &:hover {
              background-color: darken($module-hover-bg, 15%);
            }

            &.prev {
              margin-right: 1em;
            }

            &.next {
              margin-left: 1em;
            }
          }
        }
      }
    }

    > .days,
    > .weekdays {
      list-style: none;
      padding: 0;
      margin: 0;
      overflow: hidden;
      margin-bottom: .5em;

      > li {
        display: inline-block;
        float: left;
        width: calc(100% / 7);
        text-align: center;
      }
    }

    > .weekdays {
      height: 2em;
      line-height: 2em;
    }

    > .days {
      margin-bottom: 0;

      > li {
        line-height: 2.5em;

        > .other-month {
          opacity: .3;
          cursor: initial;
        }

        > .item {
          display: block;
          border-radius: 100%;

          > a {
            display: block;
            // cursor: pointer;
          }

          &:hover {
            background-color: lighten($module-hover-bg, 10%);
          }

          &.active {
            background-color: $module-hover-bg;
          }
        }
      }
    }
  }
</style>
