<template>
  <div id="app">
    <ResaDiaryV1 />
    <!-- <MyCalendarV4 v-if="false"/>
    <MyCalendarV3 v-if="false"/>
    <MyCalendarV2 v-if="false"/>
    <MyCalendar v-if="false"/> -->
  </div>
</template>

<script>
// import MyCalendar from './components/MyCalendar/MyCalendar.vue';
// import MyCalendarV2 from './components/MyCalendar/MyCalendarV2.vue';
// import MyCalendarV3 from './components/MyCalendar/MyCalendarV3.vue';
// import MyCalendarV4 from './components/MyCalendar/MyCalendarV4.vue';
import ResaDiaryV1 from './components/ResaDiary/ResaDiaryV1.vue';

export default {
  name: 'App',
  components: {
    // MyCalendar,
    // MyCalendarV2,
    // MyCalendarV3,
    // MyCalendarV4,
    ResaDiaryV1
}
}
</script>

<style>
#app {
  /* font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px; */
  background-color: #f0f0f0;
  padding: 10px;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
}
</style>
