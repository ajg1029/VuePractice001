<template>
    <div>
        <header class="title">
            <h1>Calendar Ver.3</h1>
            <div>현재 날짜/시간</div>
            <div>{{ currentDatetime }}</div>
            <button @click="testMethod">test</button>
        </header>
        <section class="options">
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
        <section class="target">
            <button v-text="'<'" @click="setPrevDate"></button>
            <div>
            {{ targetDate_YYYY }}년 {{ targetDate_MM }}월 {{ targetDate_DD }}일 {{ targetDate_DayKor }}요일
            </div>
            <button v-text="'>'" @click="setNextDate"></button>
        </section>
        <section class="calendar" v-if="calendarWeeks.length > 0">
            <MyCalendarV3cell />
            얍얍얍
        </section>
    </div>
</template>

<script>
import dayjs from 'dayjs';
import MyCalendarV3cell from './MyCalendarV3cell.vue';
export default {
    name: 'MyCalendarV3',
    components: {
        MyCalendarV3cell
    },
    data() {
        return {
            currentDatetime: new Date(),
            firstDay: null,
            benchmarkDay: null,
            
            targetDatetime: null, // Date() 객체
            targetDate: null, // YYYY-MM-DD 문자열 / '2023-01-21'

            targetDate_year: null, // 네 자리 정수 / 2023
            targetDate_month: null, // 0 ~ 11 정수 / 0
            targetDate_date: null, // 0~31 정수 / 21
            targetDate_day: null, // 0~6 정수 / 6

            targetDate_YYYY: null, // YYYY 문자열 / '2023'
            targetDate_MM: null, // MM 문자열 / '01'
            targetDate_DD: null, // DD 문자열 '21'
            targetDate_DayKor: null, // 한국어 변환 요일 '토'

            drawingDatetime: null, // Date() 객체, calendarWeeks 를 그리는 기준

            calendarWeekDays: [], // 달력 위에 표시될 요일, firstDay 에 따라 시작 요일 설정
            calendarWeeks: [], // 달력에 표시될 주들, 4주 또는 5주

			dayOptions: [
				{ text: '월', value: 1},
				{ text: '화', value: 2},
				{ text: '수', value: 3},
				{ text: '목', value: 4},
				{ text: '금', value: 5},
				{ text: '토', value: 6},
				{ text: '일', value: 0},
			],

        }
    },
    created() {
        this.updateCurrentTime()
        this.targetDatetime = this.currentDatetime
    },
    watch: {
        targetDatetime(val) {
            this.targetDate = dayjs(val).format('YYYY-MM-DD')

            this.targetDate_year = val.getFullYear()
            this.targetDate_month = val.getMonth()
            this.targetDate_date = val.getDate()
            this.targetDate_day = val.getDay()

            this.targetDate_YYYY = dayjs(val).format('YYYY')
            this.targetDate_MM = dayjs(val).format('MM')
            this.targetDate_DD = dayjs(val).format('DD')
            this.targetDate_DayKor = this.convertDayToKor(val.getDay())
        }
    },
    methods: {
        updateCurrentTime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateCurrentTime()
            }, 1000)
        },
        convertDayToKor(day) {
			switch (day) {
				case 1 : return '월'
				case 2 : return '화'
				case 3 : return '수'
				case 4 : return '목'
				case 5 : return '금'
				case 6 : return '토'
				case 0 : return '일'
			}
        },
        setPrevDate() {
            this.targetDatetime = new Date(this.targetDate_year, this.targetDate_month, this.targetDate_date - 1)
        },
        setNextDate() {
            this.targetDatetime = new Date(this.targetDate_year, this.targetDate_month, this.targetDate_date + 1)
        },
        getMonthLength() {
            return new Date(this.targetDate_year, this.targetDate_month + 1, 0).getDate()
        },
        setCalendarWeeks(datetime) {
            this.calendarWeekDays = []
            this.calendarWeeks = []

            // firstDay로부터 요일 순서 설정
            let tempWeekDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.calendarWeekDays.push(this.convertDayToKor(tempWeekDay%7))
                tempWeekDay++
            }

            // calendarWeeks 에 들어갈 week 들이 저장되는 배열
            let tempWeeks = []

            // benchmarkDay를 기준으로 week 생성
            let monthLength = this.getMonthLength()
            for (let i = 1; i <= monthLength; i++) {
                let tempDatetime = new Date(datetime.getFullYear(), datetime.getMonth(), i)
                let tempDay = tempDatetime.getDay()
                if (tempDay === this.benchmarkDay) {
                    let tempWeek = []
                    tempWeek.push(dayjs(tempDatetime).format('YYYY-MM-DD'))
                    let dateLeft = i
                    let dateRight = i
                    for (let day = tempDay; (day+7)%7 !== this.firstDay; day--) {
                        dateLeft--
                        let tempLeft = new Date(datetime.getFullYear(), datetime.getMonth(), dateLeft)
                        tempWeek.unshift(dayjs(tempLeft).format('YYYY-MM-DD'))
                    }
                    for (let day = tempDay; (day+7)%7 !== (this.firstDay+6)%7; day++) {
                        dateRight++
                        let tempRight = new Date(datetime.getFullYear(), datetime.getMonth(), dateRight)
                        tempWeek.push(dayjs(tempRight).format('YYYY-MM-DD'))
                    }
                    tempWeeks.push(tempWeek)
                }
            }
            console.log(tempWeeks)
            this.calendarWeeks = tempWeeks
    
        },
        testMethod() {
            this.setCalendarWeeks(this.targetDatetime)
        }
    }
}
</script>

<style>
.title {
    border: 1px solid gray;
    padding: 10px;
    margin: 5px;
}
.options {
    border: 1px solid gray;
    padding: 10px;
    margin: 5px;
}
.target {
    border: 1px solid gray;
    padding: 10px;
    margin: 5px;
    display: flex;
    justify-content: center;
}
.calendar {
    border: 1px solid gray;
    padding: 10px;
    margin: 5px;
}

</style>