<template>
    <div>
        <header>
            <div>currentDatetime : {{ currentDatetime }}</div>
            <div>targetDatetime : {{ targetDatetime }}</div>
        </header>
        <section>

        </section>
        <section>

        </section>
    </div>
</template>

<script>
export default {
    name: 'MyCalendarV4',
    data() {
        return{

            currentDatetime: new Date(),

            firstDay: null,
            weekArrangeType: null,
            weekNumberingType: null,

            targetDatetime: null,

            resaDate: null,
            resaMWeek: null,
            resaYWeek: null,
            resaMonth: null,
            resaYear: null,
            
            
        }
    },
    created() {
        this.firstDay = 1
        this.weekArrangeType = 3
        this.weekNumberingType = 'mw' // 'yw' 한 해의 몇 번째 주인지 / 'mw' 한 달의 몇 번째 주인지
        this.targetDatetime = new Date(this.currentDatetime.getFullYear(), this.currentDatetime.getMonth(), this.currentDatetime.getDate())
        this.updateCurrentDatetime()
    },
    methods: {
        updateCurrentDatetime() {
            setTimeout(() => {
                this.currentDatetime = new Date()
                this.updateCurrentDatetime()
            }, 1000)
        },

    }

}

</script>

<style>

</style>