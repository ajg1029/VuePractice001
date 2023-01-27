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

        </section>
        <section>
            <table>
                <thead>
                    <tr>
                        <th>{{ resaMonth }}월</th>
                        <th v-for="(day, idx) in resaDays" :key="idx">{{ day }}요일</th>
                    </tr>
                </thead>
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

            firstDay: null, // 한 주의 시작 요일(1, 2, 3, 4, 5, 6, 0)
            weekArrangeType: null, // 전체(+0) 또는 과반수(+3)
            weekNumberingType: null, // mw 또는 yw

            targetDatetime: null, // 선택 날짜, Date() 객체, 00시00분

            resaDays: null, // 달력 상단의 요일 순서, firstDay 에 따라 결정
            resaDate: null, // targetDatetime의 YYYY-MM-DD 포맷
            resaMWeek: null, // 월 기준의 week numbering
            resaYWeek: null, // 연 기준의 week numbering
            resaMonth: null, // targetDatetime 기준의 월이 아닌, resa 기준의 월
            resaYear: null, // 마찬가지로 resa 기준 연도
            resaCalendar: null, // resa 달력의 각 날짜가 들어간 배열
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
        },
        isDateIncluded(val) {
            if (val === false) {
                this.setResaCalendar(this.targetDatetime)
                this.isDateIncluded = true
            }
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
            this.setResaCalendar(this.targetDatetime)
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
                            tempYW += 1
                            tempMW += 1
                            if (datetime.getMonth() === i) {
                                tempCalendar.push([tempYW, tempMW])
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
            console.log(datetime)
            
            
            
            // console.log(datetime)
            // console.log(this.firstDay)
            // console.log(this.weekArrangeType)
            // console.log(this.weekNumberingType)
            console.log(benchmarkDay)
            console.log(tempCalendar)

        },
        testMethod() {
            this.setResaCalendar(this.targetDatetime)
        }


    }

}

</script>

<style>

</style>