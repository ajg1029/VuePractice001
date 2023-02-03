<template>
    <div>
        <header>
            <button @click="testMethod()">test</button>
            <div>currentDatetime : {{ currentDatetime }}</div>
            <div>targetDatetime : {{ targetDatetime }}</div>
            <div>
                <button v-text="'<'" @click="setPrevDate()"></button>
                {{ resaDate }}
                <button v-text="'>'" @click="setNextDate()"></button>
            </div>
        </header>
        <section>
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
        <section>
            <table>
                <thead>
                    <tr>
                        <th>{{ resaMonth }}월</th>
                        <th v-for="(day, idx) in resaDays" :key="idx">{{ day }}요일</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(week, idx) in resaCalendar" :key="idx">
                        <td></td>
                        <td v-for="(day, idx) in week" :key="idx">{{ day }}</td>
                    </tr>
                </tbody>
            </table>

        </section>
    </div>
</template>

<script>
import dayjs from 'dayjs'
export default {
    name: 'MyCalendarV4',
    data() {
        return{

            currentDatetime: new Date(), // 실시간 현재 시간

            firstDay: 1, // 한 주의 시작 요일(1, 2, 3, 4, 5, 6, 0)
            firstDayOptions: [{ text: '월', value: 1 },{ text: '일', value: 0 },{ text: '토', value: 6 }],
            weekArrangeType: +3, // 전체(+0) 또는 과반수(+3)
            weekArrangeTypeOptions: [{ text: '과반수', value: +3 }, { text: '전체', value: +0 }],
            weekNumberingType: 'mw', // mw 또는 yw
            weekNumberingTypeOptions: [{ text: 'Month Week', value: 'mw'}, { text: 'Year Week', value: 'yw'}],

            targetDatetime: null, // 선택 날짜, Date() 객체, 00시00분

            resaDays: [], // 달력 상단의 요일 순서, firstDay 에 따라 결정
            resaDate: null, // targetDatetime의 YYYY-MM-DD 포맷
            resaMWeek: null, // 월 기준의 week numbering
            resaYWeek: null, // 연 기준의 week numbering
            resaMonth: null, // targetDatetime 기준의 월이 아닌, resa 기준의 월
            resaYear: null, // 마찬가지로 resa 기준 연도
            resaCalendar: [], // resa 달력의 각 날짜가 들어간 배열
            isDateIncluded: null, // targetDatetime 이 바뀌었을 때 resaMonth 를 벗어나면 false

        }
    },
    created() {
        this.updateCurrentDatetime()
        this.firstDay = 1
        this.weekArrangeType = +3
        this.weekNumberingType = 'mw'
        this.targetDatetime = new Date(this.currentDatetime.getFullYear(), this.currentDatetime.getMonth(), this.currentDatetime.getDate())
        // this.isDateIncluded = false
    },
    watch: {
        targetDatetime(val) {
            this.resaDate = dayjs(val).format('YYYY-MM-DD')
            // iresaiDate: null, // YYYY-MM-DD 포맷 문자열 업데이트
            // iresaiMWeek: null, // 주-월넘버링
            // iresaiYWeek: null, // 주-연넘버링
            // iresaiMonth: null, // 달력 월 - 기존과 다르면 isDateIncluded false
            // iresaiYear: null, // 달력 해
            // iresaiCalendar: [], // 달력
            // this.setResaCalendar(val)


            this.checkIsDateIncluded()
            // 선택 날짜가 바뀔 때마다, 현재 표시되는 달력에 선택날짜가 포함되었는지 검사한다.
        },
        isDateIncluded(val) {
            if (val === false) {
                this.setResaCalendar(this.targetDatetime)
                this.isDateIncluded = true
            }
        },
        firstDay() {
            this.setResaCalendar(this.targetDatetime)
        },
        weekArrangeType() {
            this.setResaCalendar(this.targetDatetime)
        },
        weekNumberingType() {
            this.setResaCalendar(this.targetDatetime)
        }
    },
    methods: {
        updateCurrentDatetime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateCurrentDatetime()
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
            this.targetDatetime = new Date(this.targetDatetime.getFullYear(), this.targetDatetime.getMonth(), this.targetDatetime.getDate() - 1)
        },
        setNextDate() {
            this.targetDatetime = new Date(this.targetDatetime.getFullYear(), this.targetDatetime.getMonth(), this.targetDatetime.getDate() + 1)
        },
        checkIsDateIncluded() {
            for (let i = 0; i < this.resaCalendar.length; i++) {
                for (let j = 0; j < this.resaCalendar[i].length; j++) {
                    if (this.resaDate === this.resaCalendar[i][j]) {
                        return
                    }
                }
            }
            this.isDateIncluded = false
        },
        setResaCalendar(datetime) {
            this.resaDays = []
            this.resaCalendar = []

            let benchmarkDay = this.firstDay + this.weekArrangeType

            let tempDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.resaDays.push(this.convertDayToKor(tempDay%7))
                tempDay++
            }

            let tempCalendar = []
            // year week numbering 과 month week numbering 부터 세어야 한다...
            // target 의 month 에 도달했을 때는 month week numbering을 센다...
            let tempYW = 0
            for (let i = 0; i < 12; i++) {
                let tempMW = 0
                for (let j = 0; j < new Date(datetime.getFullYear(), i + 1, 0).getDate(); j++) {
                    if (new Date(datetime.getFullYear(), i, j).getDay() === benchmarkDay) {
                        tempYW = tempYW + 1
                        tempMW = tempMW + 1
                        if (datetime.getMonth() === i) {
                            // tempCalendar.push([tempYW, tempMW])
                            let tempWeek = []
                            for (let k = 0; k < 7; k++) {
                                tempWeek.push('0000-00-00')
                            }
                            tempCalendar.push(tempWeek)
                        }
                    }
                }
                // if (i === datetime.getMonth()) {
                //     for (let j = 0; j < new Date(datetime.getFullYear(), i + 1, 0).getDate(); j++) {
                //         if (j === benchmarkDay) {

                //         }
                //     }
                // } else {
                // }
            }
            // console.log(datetime)
            
            
            
            // console.log(datetime)
            // console.log(this.firstDay)
            // console.log(this.weekArrangeType)
            // console.log(this.weekNumberingType)
            // console.log(benchmarkDay)
            console.log(tempCalendar)
            this.resaCalendar = tempCalendar

        },
        testMethod() {
            this.setResaCalendar(this.targetDatetime)
        }


    }

}

</script>

<style>

th,td{
    padding: 10px;
    border: solid 1px lightblue;
}

</style>