<template>
    <div>
        <header>
            <button @click="testMethod()">test</button>
            <div>currentDatetime : {{ currentDatetime }}</div>
            <div>targetDatetime : {{ targetDatetime }}</div>
            <button v-text="'<'" @click="setPrevDate()"></button>
            <button v-text="'>'" @click="setNextDate()"></button>
        </header>
        <section>

        </section>
        <section>

        </section>
    </div>
</template>

<script>
import dayjs from 'dayjs'
export default {
    name: 'MyCalendarV4',
    data() {
        return{

            currentDatetime: new Date(),

            firstDay: null,
            weekArrangeType: null,
            weekNumberingType: null,

            targetDatetime: null,

            iresaiDays: null,
            iresaiDate: null,
            iresaiMWeek: null,
            iresaiYWeek: null,
            iresaiMonth: null,
            iresaiYear: null,
            iresaiCalendar: null,
            isDateIncluded: true,
            
            
            
        }
    },
    created() {
        this.updateCurrentDatetime()
        this.firstDay = 1
        this.weekArrangeType = 3
        this.weekNumberingType = 'mw' // 'yw' 한 해의 몇 번째 주인지 / 'mw' 한 달의 몇 번째 주인지
        this.targetDatetime = new Date(this.currentDatetime.getFullYear(), this.currentDatetime.getMonth(), this.currentDatetime.getDate())
    },
    watch: {
        targetDatetime(val) {
            this.iresaiDate = dayjs(val).format('YYYY-MM-DD')

            // iresaiDate: null, // YYYY-MM-DD 포맷 문자열 업데이트
            // iresaiMWeek: null, // 주-월넘버링
            // iresaiYWeek: null, // 주-연넘버링
            // iresaiMonth: null, // 달력 월 - 기존과 다르면 isDateIncluded false
            // iresaiYear: null, // 달력 해
            // iresaiCalendar: [], // 달력
        },
        isDateIncluded(val) {
            if (val === false) {
                this.setIresaiCalendar(this.targetDatetime)
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
        setIresaiCalendar(datetime) {
            this.iresaiDays = []
            this.iresaiCalendar = []

            let benchmarkDay = this.firstDay + this.weekArrangeType

            let tempDay = this.firstDay
            for (let i = 0; i < 7; i++) {
                this.iresaiDays.push(this.convertDayToKor(tempDay%7))
                tempDay++
            }

            let tempCalendar = []

            // year week numbering 과 month week numbering 부터 세어야 한다...
            let yearLength = 0
            // target 의 month 에 도달했을 때는 month week numbering을 센다...
            for (let i = 0; i < 12; i++) {
                if (i === datetime.getMonth()) {
                    for (let j = 0; j < new Date(datetime.getFullYear(), i + 1, 0).getDate(); j++) {
                        
                    }
                } else {
                    for (let j = 0; j < new Date(datetime.getFullYear(), i + 1, 0).getDate(); j++) {
                        
                    }
                }
            }

            
            
            
            // console.log(datetime)
            // console.log(this.firstDay)
            // console.log(this.weekArrangeType)
            // console.log(this.weekNumberingType)
            console.log(yearLength)
            console.log(benchmarkDay)
            console.log(tempCalendar)

        },
        testMethod() {
            this.setIresaiCalendar(this.targetDatetime)
        }


    }

}

</script>

<style>

</style>