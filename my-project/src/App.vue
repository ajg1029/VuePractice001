<template>
  <div id="app">
    
    <MyCalendarV2 />
    
    <MyCalendar v-if="false"/>
  </div>
</template>

<script>
import MyCalendar from './components/MyCalendar.vue';
import MyCalendarV2 from './components/MyCalendarV2.vue';

export default {
  name: 'App',
  components: {
    MyCalendar,
    MyCalendarV2
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
