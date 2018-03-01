<template>
  <div>
    <div id="day-bar">
      <div>Mon</div>
      <div>Tue</div>
      <div>Wed</div>
      <div>Thu</div>
      <div>Fri</div>
      <div>Sat</div>
      <div>Sun</div>
    </div>
    <div id="calendar">
      <div v-for="week in weeks" class="calendar-week">
        <calendar-day v-for="day in week" :day="day"></calendar-day>
      </div>
    </div>
  </div>
</template>

<script>
  import CalendarDay from './CalendarDay.vue';
  export default {
    computed: {
      month() {
        return this.$store.state.currentMonth;
      },
      year() {
        return this.$store.state.currentYear;
      },
      days() {
        const SUNDAY = 0;
        const MONDAY = 1;

        // Generate all days in current month
        let days = [];
        let currentDay = this.$moment(`${this.year}-${this.month}-01`, 'YYYY-MM-DD');
        do {
          days.push(currentDay);
          currentDay = this.$moment(currentDay).add(1,'days');
        } while ( (currentDay.month() + 1) === this.month );

        // Pad first week with previous days starting at Monday
        currentDay = this.$moment(days[0]);
        if (currentDay.day() !== MONDAY) {
          do {
            currentDay = this.$moment(currentDay).subtract(1,'days');
            days.unshift(currentDay);
          } while ( currentDay.day() !== MONDAY );
        }

        // Pad final week with extra days ending with Sunday
        currentDay = this.$moment(days[days.length - 1]);
        if (currentDay.day() !== SUNDAY) {
          do {
            currentDay = this.$moment(currentDay).add(1,'days');
            days.push(currentDay);
          } while ( currentDay.day() !== SUNDAY );
        }

        return days;
      },
      weeks() {
        let weeks = [];
        let week = [];
        for (let day of this.days) {
            week.push(day);
            if (week.length === 7) {
              weeks.push(week);
              week = [];
            }
        }
        return weeks;
      }
    },
    components: {
      CalendarDay
    }
  }
</script>
