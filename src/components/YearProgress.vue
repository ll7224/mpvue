<template>
    <div class="progressBar">
        <progress :percent="percent" activeColor='#EA5A49' show-info />
        <p class="warn-content">{{year}}已经过去了{{days}}天, {{percent}}%</p>
    </div>
</template>
<script>
export default {
  methods: {
    isLeapYear() {
      const year = new Date().getFullYear();
      if (year % 400 === 0) {
        return true;
      } else if (year % 4 === 0 && year % 100 !== 0) {
        return true;
      }
      return false;
    },
    getDayOfYear() {
      return this.isLeapYear() ? 366 : 365;
    },
  },
  computed: {
    year() {
      return new Date().getFullYear();
    },
    days() {
      const start = new Date();
      start.setMonth(0);
      start.setDate(0);
      // start就是今年的第一天
      // 今天的时间戳 减去今年第一天的时间戳
      const offset = new Date().getTime() - start.getTime();
      return parseInt(offset / 1000 / 60 / 60 / 24, 10) + 1;
    },
    percent() {
      return ((this.days * 100) / this.getDayOfYear()).toFixed(1);
    },
  },
};
</script>
<style lang="stylus" scoped>
  .progressBar
    position: relative;
    left: 10%
    width: 80%;
    text-align: center;
    margin-top: 10px;
    margin-bottom: 30px;
    .warn-content
       font-size: 15px
</style>
