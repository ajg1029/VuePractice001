<template>
    <div>
        <header class="header">
            <!-- <button @click="testMethod">TEST</button> -->
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
                        <td>{{ targetBenchmarkDatetime.getFullYear() }}년 {{ week.yw }}주<br />{{ targetBenchmarkDatetime.getMonth() + 1 }}월 {{ week.mw }}주</td>
                        <td v-for="(day, idx) in week.days" :key="idx" @click="setTarget(day.datetime)">{{ day.date }}</td>
                    </tr>
                </tbody>
            </table>

        </section>

    </div>
</template>

<script>
import dayjs from 'dayjs';

export default {
    name: 'ResaDiaryV2',
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
            targetFirstDayDatetime: null,
            targetBenchmarkDatetime: null,
            targetDate: null,

            resa: {
                days: [],
                calendar: [],
            },
        }
    },
    created() {
        this.updateCurrentDatetime()
        this.targetDatetime = new Date(this.currentDatetime.getFullYear(), this.currentDatetime.getMonth(), this.currentDatetime.getDate())
    },
    watch: {
        targetDatetime(val) {
            this.setResa()
            this.targetDate = dayjs(val).format('YYYY-MM-DD')
            this.checkTarget()
        },
        firstDay() {
            this.setResa()
            this.setCalendar()
        },
        weekArrangeType() {
            this.setResa()
            this.setCalendar()
        },
        weekNumberingType() {
            
        }
    },
    methods: {
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
        setTarget(datetime) {
            this.targetDatetime = datetime
        },
        setCalendar() {
            console.log('setting calendar...')

            this.resa.days = []
            let tempDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.resa.days.push(this.convertDayToKor(tempDay%7))
                tempDay++
            }

            this.resa.calendar = []
            let benchmarkDay = (this.firstDay + this.weekArrangeType) % 7
            let tempCalendar = []
            let tempYW = 0
            for (let i = 0; i < 12; i++) {
                let tempMW = 0
                for (let j = 0; j < new Date(this.targetBenchmarkDatetime.getFullYear(), i + 1, 0).getDate(); j++) {
                    if (new Date(this.targetBenchmarkDatetime.getFullYear(), i, j).getDay() === benchmarkDay) {
                        tempYW = tempYW + 1
                        tempMW = tempMW + 1
                        if (this.targetBenchmarkDatetime.getMonth() === i) {
                            let tempWeek = []
                            for (let k = 0; k < 7; k++) {
                                tempWeek.push({
                                    date: dayjs(new Date(this.targetBenchmarkDatetime.getFullYear(), i, j - this.weekArrangeType + k)).format('YYYY-MM-DD'),
                                    datetime: new Date(this.targetBenchmarkDatetime.getFullYear(), i, j - this.weekArrangeType + k)
                                })
                            }
                            tempCalendar.push({
                                mw: tempMW,
                                yw: tempYW,
                                days: tempWeek,
                            })
                        }
                    }
                }
            }
            console.log(tempCalendar)
            this.resa.calendar = tempCalendar

            console.log('done')
        },
        checkTarget() {
            console.log('선택한 날짜가 현재 표시된 달력에 포함되었는지 확인 중...')
            for (let i = 0; i < this.resa.calendar.length; i++) {
                for (let j = 0; j < this.resa.calendar[i].days.length; j++) {
                    if (this.targetDate === this.resa.calendar[i].days[j].date) {
                        console.log('있구만')
                        return
                    }
                }
            }
            console.log('선택한 날짜가 현재 표시된 달력 안에 없음. 달력 새로 그리자')
            console.log(this.targetDatetime)
            console.log(this.targetBenchmarkDatetime)
            this.setCalendar()
        },
        setResa() {
            let tempFirstDayDatetiime = this.targetDatetime
            while (tempFirstDayDatetiime.getDay() !== this.firstDay) {
                tempFirstDayDatetiime = new Date(tempFirstDayDatetiime.getFullYear(), tempFirstDayDatetiime.getMonth(), tempFirstDayDatetiime.getDate() - 1)
            }
            if (tempFirstDayDatetiime.getDay() === this.firstDay) {
                this.targetFirstDayDatetime = new Date(tempFirstDayDatetiime.getFullYear(), tempFirstDayDatetiime.getMonth(), tempFirstDayDatetiime.getDate())
            }
            this.targetBenchmarkDatetime = new Date(this.targetFirstDayDatetime.getFullYear(), this.targetFirstDayDatetime.getMonth(), this.targetFirstDayDatetime.getDate() + this.weekArrangeType)
        },
        // testMethod() {
        //     this.setCalendar()
        // }



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