<template>
    <div>
        <h1>Calendar Ver.2</h1>
        <section>
            <button @click="testMethod">test</button>
            <div>현재 날짜/시간</div>
            <div>{{ currentDatetime }}</div>
            <br />
            <div>선택한 날짜</div>
            <div>{{ selectedDatetime }}</div>
            <div>{{ selectedYear }}년</div>
            <div>{{ selectedMonth + 1}}월</div>
            <div>{{ selectedDate }}일</div>
            <div>{{ convertDayNumToKor(selectedDay) }}요일</div>
            <!-- <button v-text="'<<'"></button> -->
            <button @click="setPrevDay" v-text="'<'"></button>
            <button @click="setNextDay" v-text="'>'"></button>
            <!-- <button v-text="'>>'"></button> -->
            <br />
            <div>{{ isInIncluded }}</div>
			<div>
				<div>한 주의 시작 요일</div>
				<label v-for="option in dayOptions" :key="option.value">
					<input type="radio" v-model="firstDay" :value="option.value"> {{ option.text }}
				</label>
			</div>

			<div>
				<div>기준 요일 (어떤 달에 포함될 지)</div>
				<label v-for="option in dayOptions" :key="option.value">
					<input type="radio" v-model="benchmarkDay" :value="option.value"> {{ option.text }}
				</label>
			</div>
        </section>
        <hr />
        <section>
            <table>
                <thead>
                    <tr>
                        <th>{{ selectedMonth + 1 }}월</th>
                        <th v-for="(day, idx) in displayWeekDays" :key="idx">{{ day }}요일</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(week, idx) in displayWeeks" :key="idx">
                        <td>week{{ idx + 1 }}</td>
                        <td v-for="(date, idx) in week" :key="idx">{{ date }}</td>
                    </tr>
                </tbody>
            </table>


        </section>


        <section>

            <MyCalendarV2Cell />

        </section>
        

    </div>
</template>

<script>
import dayjs from 'dayjs';
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

            firstDay: 1,
            benchmarkDay: 4,

            selectedDate: null,
            selectedDay: null, // 1, 2, 3, 4, 5, 6, 0
            selectedMonth: null, // 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11
            selectedYear: null, // 'YYYY'

            displayWeekDays: [],
            displayWeeks: [],
            displayPrevMonthLastDate: null,
            displayNextMonthFirstDate: null,
            
			dayOptions: [
				{ text: '월', value: 1},
				{ text: '화', value: 2},
				{ text: '수', value: 3},
				{ text: '목', value: 4},
				{ text: '금', value: 5},
				{ text: '토', value: 6},
				{ text: '일', value: 0},
			],

            isInIncluded: true,
        }
    },
    watch: {
        selectedDatetime(val) {
            this.selectedYear = val.getFullYear()
            this.selectedMonth = val.getMonth()
            this.selectedDate = val.getDate()
            this.selectedDay = val.getDay()
            this.displayMonthlyWeeks(this.selectedDatetime)

        },
    },
    created() {
        this.updateRealtime()
        this.selectedDatetime = this.currentDatetime
        this.isInThisMonth = true

    },
    methods: {
        updateRealtime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateRealtime()
            }, 1000);
        },
        convertDayNumToKor(dayNum) {
			switch (dayNum) {
				case 1 : return '월'
				case 2 : return '화'
				case 3 : return '수'
				case 4 : return '목'
				case 5 : return '금'
				case 6 : return '토'
				case 0 : return '일'
			}
        },
        getFirstAndLastDates(datetime) {
            return {
                prevMonthLast: new Date(datetime.getFullYear(), datetime.getMonth(), 0),
                thisMonthFirst: new Date(datetime.getFullYear(), datetime.getMonth(), 1),
                thisMonthLast: new Date(datetime.getFullYear(), datetime.getMonth() + 1, 0),
                nextMonthFirst: new Date(datetime.getFullYear(), datetime.getMonth() + 1, 1),
            }
        },
        setPrevDay() {
            this.selectedDatetime = new Date(this.selectedYear, this.selectedMonth, this.selectedDate - 1)
        },
        setNextDay() {
            this.selectedDatetime = new Date(this.selectedYear, this.selectedMonth, this.selectedDate + 1)
        },
        displayMonthlyWeeks(datetime) {
            this.displayWeekDays = []
            let tempWeekDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.displayWeekDays.push(this.convertDayNumToKor(tempWeekDay%7))
                tempWeekDay++
            }

            let tempWeeks = []
            let monthLength = parseInt(dayjs(this.getFirstAndLastDates(datetime).thisMonthLast).format('DD'))
            for (let i = 1; i <= monthLength; i++) {
                // console.log(i)
                let tempDatetime = new Date(this.selectedYear, this.selectedMonth, i)
                let tempDay = parseInt(dayjs(tempDatetime).format('d'))
                if (tempDay === this.benchmarkDay) {
                    // console.log(tempDatetime)
                    let tempWeek = []
                    tempWeek.push(dayjs(tempDatetime).format('YYYY-MM-DD'))
                    let dateLeft = i
                    let dateRight = i

                    for (let day = tempDay; (day+7)%7 !== this.firstDay; day--) {
                        dateLeft--
                        let tempDate = new Date(this.selectedYear, this.selectedMonth, dateLeft)
                        tempWeek.unshift(dayjs(tempDate).format('YYYY-MM-DD'))
                    }

                    for (let day = tempDay; (day+7)%7 !== (this.firstDay+6)%7; day++) {
                        dateRight++
                        let tempDate = new Date(this.selectedYear, this.selectedMonth, dateRight)
                        tempWeek.push(dayjs(tempDate).format('YYYY-MM-DD'))
                    }
                    tempWeeks.push(tempWeek)
                }
            }
            console.log(tempWeeks)
            this.displayWeeks = tempWeeks
            this.isInThisMonth = true
        },
        checkIsIncluded() {
            for (let i = 0; i < this.displayWeeks.length; i++) {
                for (let j = 0; j < this.displayWeeks[i].length; j++) {
                    if (dayjs(this.selectedDatetime).format('YYYY-MM-DD') === this.displayWeeks[i][j]) {
                        return
                    }
                }
            }
            this.isInIncluded = false
        },
        testMethod() {
            // this.displayMonthlyWeeks(this.selectedDatetime)
        }
    }
}

// 변수 이름을 명사형으로 하자...
// displayWeeks 보다는 diplayedWeeks 또는 weeksDisplayed, weeksOnDisplay, ...

// isIncluded, selectedDatetime, displayWeeks 가 서로를 참조하고 있어서 막 얽혀있음
// ver1 의 문제점을 해결하지 못했다.
// selectedDatetime 하나만으로는 부족한 것 같다.
// 실제로 선택한 날짜로서의 selectedDatetime이 하나 필요하고, 이것으로 weeks 를 구성한다.
// 그리고 YYYY-MM-DD 로 표시되는 또다른 datetime이 필요하다. 이것으로 넘기기를 구현하자.


</script>

<style>

</style>