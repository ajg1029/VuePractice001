<template>
	<div>
		<div>{{ test1 }}</div>
		<div>{{ test2 }}</div>
		<div>{{ weekdays }}</div>
		<button @click="testMethod">test btn</button>
		<div>{{ new Date().getTime() }}</div>
		<div>{{ new Date().setDate(1) }}</div>
		<hr />

		<section class="sectionCalendar">
			<div>현재 날짜 : {{ currentYear }}년 {{ currentMonth + 1 }}월 {{ currentDate }}일 {{ currentDay }}</div>
			<div>요일 테스트 : {{  }}</div>
			<table>
				<thead>
					<tr>
						<th v-for="(day, idx) in weekdays" :key="idx">{{ day }}</th>
					</tr>
				</thead>
				<tbody>
					<tr>
						<td>1</td>
						<td>2</td>
						<td>3</td>
						<td>4</td>
						<td>5</td>
						<td>6</td>
						<td>7</td>
					</tr>
				</tbody>
			</table>
			
		</section>

	</div>
</template>

<script>
import dayjs from 'dayjs'

export default {
	name: 'MyCalendar',
	data() {
		return {
			test1: dayjs(new Date()).format('ddd'),
			test2: new Date().setFullYear(),

			// 달력을 한번 만들어보자
			selectedRootDate: null, // 선택한 날짜
			currentRootDate: new Date(), // 현지 시간 기준 현재 날짜
			firstDay: 1, // 한 주의 시작 요일 / 대한민국 표준 - 월요일(1)
			benchmarkDay: 4, // 어떤 요일을 기준으로 해당 주가 어떤 달에 들어갈지 결정 / 대한민국 표준 - 목요일(4) / 한 주의 과반수
			weekdays: [], // firstDay 를 기준으로 정렬된 요일을 넣는다.
			selectedYear: null,
			selectedMonth: null,
			selectedDate: null,
			selectedDay: null,
			weeks: []
		}
	},
	created() {
		// 선택한 날짜의 기본값 : 현재 날짜
		this.selectedRootDate = this.currentRootDate

		// 한 주의 시작 요일을 기준으로 일곱 요일 구성
		let tempWeekDay = this.firstDay
		for (let i = 0; i < 7; i++) {
			this.weekdays.push(this.dayKor(tempWeekDay%7))
			tempWeekDay++
		}
	},
	methods: {
		testMethod() {
			// console.log(this.getFirstDateOfMonth(this.currentRootDate).getDay())
			this.getWeeks(new Date())
		},
		// 선택한 날짜의 연, 월, 일, 요일을 반환하는 메서드
		yearSelect() {this.selectedYear = this.selectedRootDate.getFullYear()},
		monthSelected() {this.selectedMonth = this.selectedRootDate.getMonth()},
		dateSelect() {this.selectedDate = this.selectedRootDate.getDate()},
		daySelect() {this.selectedDay = dayjs(this.selectedRootDate).format('ddd')},

		// 요일의 숫자값을 한국어 문자열로 바꿔주는 메서드
		dayKor(day) {
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

		// 날짜에 해당하는 달의 1일과 말일
		getLastDateOfPrevMonth(date) {return new date(date.getFullYear(), date.getMonth(), 0)},
		getFirstDateOfMonth(date) {return new Date(date.getFullYear(), date.getMonth(), 1)},
		getLastDateOfMonth(date) {return new Date(date.getFullYear(), date.getMonth() + 1, 0)},
		getFirstDateOfNextMonth(date) {return new Date(date.getFullYear(), date.getMonth() + 1, 1)},

		getWeeks(date) {
			let tempWeeks = []
			for (let dd = 1; dd <= this.getLastDateOfMonth(date).getDate(); dd++) {
				let tempDate = date
				let tempDay = new Date(date.getFullYear(), date.getMonth(), dd).getDay()
				if (tempDay === this.benchmarkDay ) {
					let tempWeek = []
					for (let day = tempDay; (day+7)%7 !== this.firstDay; day--){
						tempWeek.unshift(tempDate)
						tempDate.setDate
					}
					tempWeeks.push(tempWeek)
				}
			}
			console.log(tempWeeks)
		}
	},
	computed: {
		currentYear() {return this.currentRootDate.getFullYear()},
		currentMonth() {return this.currentRootDate.getMonth()},
		currentDate() {return this.currentRootDate.getDate()},
		currentDay() {return dayjs(this.currentRootDate).format('d')}
	}
	
}

</script>

<style>
.test {
	display: none;
}

</style>