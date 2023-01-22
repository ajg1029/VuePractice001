<template>
  <div id="app">
    <MyCalendarV4 />

    <MyCalendarV3 v-if="false"/>
    
    <MyCalendarV2 v-if="false"/>

    <MyCalendar v-if="false"/>
  </div>
</template>

<script>
import MyCalendar from './components/MyCalendar.vue';
import MyCalendarV2 from './components/MyCalendarV2.vue';
import MyCalendarV3 from './components/MyCalendarV3.vue';
import MyCalendarV4 from './components/MyCalendarV4.vue';

export default {
  name: 'App',
  components: {
    MyCalendar,
    MyCalendarV2,
    MyCalendarV3,
    MyCalendarV4
}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
