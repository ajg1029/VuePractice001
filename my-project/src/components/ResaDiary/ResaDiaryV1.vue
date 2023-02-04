<template>
    <div>
        <header class="header">
            <button @click="testMethod">TEST</button>
            <div>currentDatetime : {{ currentDatetime }}</div>
            <div>targetDatetime : {{ targetDatetime }}</div>
            <div>
                <button v-text="'<'" @click="setPrevDate()"></button>
                {{ targetDate }}
                <button v-text="'>'" @click="setNextDate()"></button>
            </div>
        </header>
        <section class="options">
            <div>
                <div>First Day</div>
                <label v-for="option in firstDayOptions" :key="option.value">
                    <input type="radio" v-model="firstDay" :value="option.value">
                    {{ option.text }}
                </label>
            </div>
            <div>
                <div>Week Arrange Type</div>
                <label v-for="option in weekArrangeTypeOptions" :key="option.value">
                    <input type="radio" v-model="weekArrangeType" :value="option.value">
                    {{ option.text }}
                </label>
            </div>
            <div>
                <div>Week Numbering Type</div>
                <label v-for="option in weekNumberingTypeOptions" :key="option.value">
                    <input type="radio" v-model="weekNumberingType" :value="option.value">
                    {{ option.text }}
                </label>
            </div>
        </section>
        <section class="calendar">
            <table>
                <thead>
                    <tr>
                        <th></th>
                        <th v-for="(day, idx) in resa.days" :key="idx">{{ day }}요일</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(week, idx) in resa.calendar" :key="idx">
                        <td></td>
                        <td v-for="(day, idx) in week" :key="idx">{{ day.date }}</td>
                    </tr>
                </tbody>
            </table>

        </section>
    </div>
</template>

<script>
import dayjs from 'dayjs'

export default {
    name: 'ResaDiary',
    data() {
        return {
            currentDatetime: new Date(),

            firstDay: 1,
            firstDayOptions: [{ text: '월', value: 1 },{ text: '일', value: 0 },{ text: '토', value: 6 }],
            weekArrangeType: +3,
            weekArrangeTypeOptions: [{ text: '과반수', value: +3 }, { text: '전체', value: +0 }],
            weekNumberingType: 'mw',
            weekNumberingTypeOptions: [{ text: 'Month Week', value: 'mw'}, { text: 'Year Week', value: 'yw'}],

            targetDatetime: null,
            targetDate: null,
            targetFirstDayDatetime: null,
            targetBenchmarkDatetime: null,

            resa: {
                days: [],
                calendar: [],
            },

            isIncluded: null,

        }
    },
    created() {
        this.updateCurrentDatetime()
        this.targetDatetime = new Date(this.currentDatetime.getFullYear(), this.currentDatetime.getMonth(), this.currentDatetime.getDate())
    },
    watch: {
        targetDatetime(val) {
            this.targetDate = dayjs(val).format('YYYY-MM-DD')
        },
        isIncluded() {

        },
        firstDay() {
            this.setCalendar()
        },
        weekArrangeType() {
            this.setCalendar()
        },
        weekNumberingType() {
            this.setCalendar()
        }
    },
    methods: {
        testMethod() {
            // console.log()
            this.setCalendar()
        },
        updateCurrentDatetime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateCurrentDatetime()
            }, 1000)
        },
        setPrevDate() {
            this.targetDatetime = new Date(this.targetDatetime.getFullYear(), this.targetDatetime.getMonth(), this.targetDatetime.getDate() - 1)
        },
        setNextDate() {
            this.targetDatetime = new Date(this.targetDatetime.getFullYear(), this.targetDatetime.getMonth(), this.targetDatetime.getDate() + 1)
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
        setCalendar() {
            console.log('setting calendar...')
            // 달력 상단에 표시되는 요일과 달력 내부 날짜 초기화
            this.resa.days = []
            this.resa.calendar = []
            
            // 달력 상단 요일 세팅
            let tempDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.resa.days.push(this.convertDayToKor(tempDay%7))
                tempDay++
            }

            // 선택 날짜가 포함된 주의 첫 요일 검색 및 저장
            let tempFirstDatetime = this.targetDatetime
            while (tempFirstDatetime.getDay() !== this.firstDay) {
                tempFirstDatetime = new Date(tempFirstDatetime.getFullYear(), tempFirstDatetime.getMonth(), tempFirstDatetime.getDate() - 1)
                if (tempFirstDatetime.getDay() === this.firstDay) {
                    this.targetFirstDayDatetime = new Date(tempFirstDatetime.getFullYear(), tempFirstDatetime.getMonth(), tempFirstDatetime.getDate())
                }
            }

            // 선택 날짜가 포함된 주의 기준 요일 저장
            let benchmarkDay = (this.firstDay + this.weekArrangeType) % 7
            this.targetBenchmarkDatetime = new Date(this.targetFirstDayDatetime.getFullYear(), this.targetFirstDayDatetime.getMonth(), this.targetFirstDayDatetime.getDate() + this.weekArrangeType)

            //
            let tempCalendar = []
            let tempYW = 0
            for (let i = 0; i < 12; i++) {
                let tempMW = 0
                // console.log(i + 1, '월')
                for (let j = 0; j < new Date(this.targetBenchmarkDatetime.getFullYear(), i + 1, 0).getDate(); j++) {
                    if (new Date(this.targetBenchmarkDatetime.getFullYear(), i, j).getDay() === benchmarkDay) {
                        tempYW = tempYW + 1
                        tempMW = tempMW + 1
                        // console.log('week')
                        if (this.targetBenchmarkDatetime.getMonth() === i) {
                            // console.log('month week')
                            let tempWeek = []
                            for (let k = 0; k < 7; k++) {
                                // console.log('day')
                                tempWeek.push({
                                    date: dayjs(new Date(this.targetBenchmarkDatetime.getFullYear(), i, j - this.weekArrangeType + k)).format('YYYY-MM-DD'),
                                    datetime: new Date(this.targetBenchmarkDatetime.getFullYear(), i, j - this.weekArrangeType + k)
                                })
                            }
                            // console.log(tempWeek)
                            tempCalendar.push(tempWeek)
                        }
                    }
                }
            }
            // console.log('targetDatetime : ', this.targetDatetime)
            // console.log('targetFirstDayDatetime : ', this.targetFirstDayDatetime)
            // console.log('targetBenchmarkDatetime : ', this.targetBenchmarkDatetime)
            // console.log(tempCalendar)
            this.resa.calendar = tempCalendar

            // console.log(this.resa.days)
            // console.log(this.resa.calendar)
            console.log('done')
        }

    }
}
</script>

<style scoped>
.header{
    margin: 10px;
    padding: 10px;
    background-color: #ffffff;
}

.options{
    margin: 10px;
    padding: 10px;
    background-color: #ffffff;
}
.calendar{
    margin: 10px;
    padding: 10px;
    background-color: #ffffff;
}


th,td{
    padding: 10px;
    border: solid 1px #505050;
}
</style>