<template>
    <div>
        <h1>Calendar Ver.2</h1>
        <section>
            <div>현재 날짜/시간</div>
            <div>{{ currentDatetime }}</div>
            <br />
            <div>선택한 날짜</div>
            <div>{{ selectedDatetime }}</div>
            <div>{{ selectedYear }}년</div>
            <div>{{ selectedMonth + 1}}월</div>
            <div>{{ selectedDate }}일</div>
            <div>{{ convertDayNumToKor(selectedDay) }}요일</div>
        </section>


        <section>

            <MyCalendarV2Cell />

        </section>
        

    </div>
</template>

<script>
import MyCalendarV2Cell from './MyCalendarV2Cell.vue';
export default {
    name: 'MyCalendarV2',
    components: {
        MyCalendarV2Cell
    },
    data() {
        return {
            currentDatetime: new Date(),
            selectedDatetime: null, // Date() 객체

            firstDay: null,
            benchmarkDay: null,

            selectedDate: null,
            selectedDay: null, // 1, 2, 3, 4, 5, 6, 0
            selectedMonth: null, // 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11
            selectedYear: null, // 'YYYY'

            displayWeeks: [],
            displayPrevMonthLastDate: null,
            displayNextMonthFirstDate: null,
            
        }
    },
    created() {
        this.updateRealtime()
        this.selectedDatetime = this.currentDatetime
        this.setSelectedDateInfo(this.selectedDatetime)
    },
    methods: {
        updateRealtime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateRealtime()
            }, 1000);
        },
        setSelectedDateInfo(datetime) {
            this.selectedYear = datetime.getFullYear()
            this.selectedMonth = datetime.getMonth()
            this.selectedDate = datetime.getDate()
            this.selectedDay = datetime.getDay()
        },
        convertDayNumToKor(day) {
			switch (day) {
				case 1 : return '월'
				case 2 : return '화'
				case 3 : return '수'
				case 4 : return '목'
				case 5 : return '금'
				case 6 : return '토'
				case 0 : return '일'
			}
        }
    }
}

</script>

<style>

</style>