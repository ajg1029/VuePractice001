<template>
	<div class="myCalendar">
		<div style="display:none">
			<div>{{ test1 }}</div>
			<div>{{ test2 }}</div>
			<div>{{ weekdays }}</div>
			<div>{{ new Date().getTime() }}</div>
			<div>{{ new Date().setDate(1) }}</div>
			<hr />
		</div>
	
		<button @click="testMethod">TEST</button>

		<section class="sectionCalendar">
			<div>현재 날짜 : {{ currentYear }}년 {{ currentMonth + 1 }}월 {{ currentDate }}일 {{ currentDay }}</div>
			<!-- <div>선택 날짜 : {{ selectedYear }}년 {{ selectedMonth + 1 }}월 {{ selectedDate }}일 {{ selectedDay }}</div> -->
			<div>{{ selectedRootDate.getFullYear() }}년</div>
			<div>{{ selectedRootDate.getMonth() + 1}}월</div>
			<div>{{ selectedRootDate.getDate() }}일</div>
			<hr />

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

			<hr />

			<h4>현재 날짜 : {{ currentYear }}년 {{ currentMonth + 1 }}월 {{ currentDate }}일 {{ dayKor(parseInt(currentDay)) }}요일</h4>
			<br />
			<div>
				<button @click="selectedRootDatePrev">-</button>
				<button @click="selectedRootDateNext">+</button>
			</div>
			<div>
				<button v-text="'<'" @click="displayPrevMonth"></button>
				<button v-text="'>'" @click="displayNextMonth"></button>
			</div>
			<br />
			<br />
			<table>
				<thead>
					<tr>
						<th></th>
						<th v-for="(day, idx) in weekdays" :key="idx">{{ day }}</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(week, idx) in weeks" :key="idx">
						<td>
							<!-- <div>year0000 week0</div> -->
							<div>{{ currentMonth + 1 }}월 / week{{ idx + 1 }}</div>
						</td>
						<td v-for="(date, idx) in week" :key="idx">{{ date }}</td>
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
			weeks: [],

			dayOptions: [
				{ text: '월', value: 1},
				{ text: '화', value: 2},
				{ text: '수', value: 3},
				{ text: '목', value: 4},
				{ text: '금', value: 5},
				{ text: '토', value: 6},
				{ text: '일', value: 0},
			]
		}
	},
	created() {
		// 선택한 날짜의 기본값 : 현재 날짜
		this.selectedRootDate = new Date()
		// 선택한 날짜가 포함된 달의 달력을 표시
		// this.getDisplayedWeeks(this.selectedDate)
		// this.getWeekDays()
		// console.log(this.selectedRootDate)
	},
	watch: {
		selectedRootDate(val) {
			this.getDisplayedWeeks(val)
			this.getWeekDays()
		}
	},
	methods: {
		testMethod() {
			// console.log(this.getFirstDateOfMonth(this.currentRootDate).getDay())
			// console.log(this.selectedRootDate)
			this.getDisplayedWeeks(this.selectedRootDate)
			this.getWeekDays()
		
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

		// first, benchmark
		getDisplayedWeeks(rootDate) { // 선택 날짜, 한 주의 시작 요일, 한 주의 기준 요일 --> 선택 날짜가 포함된 달의 주(weeks)들
			let tempWeeks = [] // weeks 에 들어갈 week 들이 저장될 배열
			for (let dd = 1; dd <= this.getLastDateOfMonth(rootDate).getDate(); dd++) { // 선택 날짜가 포함된 달의 1일부터 말일까지 순회
				// let tempDate = rootDate.setDate(dd)
				// setDate를 사용하면 원래 값이 변한다....
				let tempDate = new Date(rootDate.getFullYear(), rootDate.getMonth(), dd)
				let tempDay = parseInt(dayjs(tempDate).format('d'))

				if (tempDay === this.benchmarkDay ) {
					// console.log(this.dayKor(tempDay))
					let tempWeek = [new Date(rootDate.getFullYear(), rootDate.getMonth(), dd).getDate()]
					let ddLeft = dd
					let ddRight = dd

					for (let day = tempDay; (day+7)%7 !== (this.firstDay+7)%7; day--) {
						ddLeft--
						tempWeek.unshift(new Date(rootDate.getFullYear(), rootDate.getMonth(), ddLeft).getDate())
					}
					for (let day = tempDay; (day+7)%7 !== (this.firstDay+7-1)%7; day++) {
						ddRight++
						tempWeek.push(new Date(rootDate.getFullYear(), rootDate.getMonth(), ddRight).getDate())
					}
					
					// for (let day = tempDay; (day+7+1)%7 !== (this.firstDay+7)%7; day--) {
					// 	tempWeek.unshift(dayjs(rootDate.setDate(ddLeft)).format('YYYY-MM-DD'))
					// 	ddLeft--
					// }
					// for (let day = tempDay; (day+7)%7+1 !== (this.firstDay+7)%7; day++ ) {
					// 	tempWeek.push(dayjs(rootDate.setDate(ddFiveSixSeven)).format('YYYY-MM-DD'))
					// 	ddFiveSixSeven++
					// }
					tempWeeks.push(tempWeek)
				}
			}
			console.log(rootDate)
			// console.log(tempWeeks)
			this.weeks = tempWeeks
		},
		getWeekDays() {
			// 한 주의 시작 요일을 기준으로 일곱 요일 구성
			this.weekdays = []
			let tempWeekDay = this.firstDay
				for (let i = 0; i < 7; i++) {
					this.weekdays.push(this.dayKor(tempWeekDay%7))
					tempWeekDay++
				}
		},
		displayPrevMonth() {
			// if (!this.weeks.length) {return}
			let tempPrev = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth() - 1, this.selectedRootDate.getDate())
			if (tempPrev.getDate() !== this.selectedRootDate.getDate()) {
				tempPrev = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth(), 0)
			}
			this.selectedRootDate = tempPrev

		},
		displayNextMonth() {
			// if (!this.weeks.length) {return}
			let tempNext = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth() + 1, this.selectedRootDate.getDate())
			if (tempNext.getDate() !== this.selectedRootDate.getDate()) {
				tempNext = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth() + 2, 0)
			}
			this.selectedRootDate = tempNext
		},
		selectedRootDatePrev() {
			let temp = this.selectedRootDate.getDate()
			// this.selectedRootDate.setDate(temp - 1)
			this.selectedRootDate = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth(), temp - 1)

		},
		selectedRootDateNext() {
			let temp = this.selectedRootDate.getDate()
			// this.selectedRootDate.setDate(temp + 1)
			// vue 의 data() 에는 영향을 미치지 못해서, 바인딩이 제대로 안 됨. 아래와 같이 다시 할당을 해줘야...
			this.selectedRootDate = new Date(this.selectedRootDate.getFullYear(), this.selectedRootDate.getMonth(), temp + 1)
		},




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
.myCalendar {
	display: flex;
	flex-direction: column;
	align-items: center;
}

</style>