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
                    <tr>

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
            this.setCalendar(this.targetDatetime)
        },
        weekArrangeType() {
            this.setCalendar(this.targetDatetime)
        },
        weekNumberingType() {
            this.setCalendar(this.targetDatetime)
        }
    },
    methods: {
        testMethod() {
            // console.log()
            this.setCalendar(this.targetDatetime)
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
        setCalendar(datetime) {
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
            let tempFirstDatetime = datetime
            while (tempFirstDatetime.getDay() !== this.firstDay) {
                tempFirstDatetime = new Date(tempFirstDatetime.getFullYear(), tempFirstDatetime.getMonth(), tempFirstDatetime.getDate() - 1)
                if (tempFirstDatetime.getDay() === this.firstDay) {
                    this.targetFirstDayDatetime = new Date(tempFirstDatetime.getFullYear(), tempFirstDatetime.getMonth(), tempFirstDatetime.getDate())
                }
            }

            // 선택 날짜가 포함된 주의 기준 요일 저장
            this.targetBenchmarkDatetime = new Date(this.targetFirstDayDatetime.getFullYear(), this.targetFirstDayDatetime.getMonth(), this.targetFirstDayDatetime.getDate() + this.weekArrangeType)

            //







            console.log('targetDatetime : ', this.targetDatetime)
            console.log('targetFirstDayDatetime : ', this.targetFirstDayDatetime)
            console.log('targetBenchmarkDatetime : ', this.targetBenchmarkDatetime)

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