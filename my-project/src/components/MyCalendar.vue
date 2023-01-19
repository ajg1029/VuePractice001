<template>
	<div>

		<div class="test">
			<!-- <div v-for="(item, idx) in testArray" :key="idx">{{ item }}</div> -->
			<!-- <button @click="doSthWithTodayDate">test</button> -->
		</div>

		<section class="sectionCalendar">
			<div>현재 날짜 : {{ currentYear }}년 {{ currentMonth + 1 }}월 {{ currentDate }}일 {{ currentDay }}</div>
			<div>요일 테스트 : {{  }}</div>
			<table>
				<thead>
					<tr>
						<th>월</th>
						<th>화</th>
						<th>수</th>
						<th>목</th>
						<th>금</th>
						<th>토</th>
						<th>일</th>
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
			// // Date object, dayjs
			// testArray: [
			// 	new Date(),
			// 	dayjs(new Date()).format('YYYY-MM-DD'),
			// 	new Date().getDate(),
			// 	Date.now(),
			// ],
			// todayDate: new Date(),
			// theDate: new Date(1994, 7, 23, 15, 56),

			// 달력을 한번 만들어보자
			selectedRootDate: null, // 선택한 날짜
			currentRootDate: new Date(), // 현지 시간 기준 현재 날짜
			firstDay: 1, // 한 주의 시작 요일 / 국가 표준 - 월요일
			benchmarkDay: 4, // 어떤 요일을 기준으로 해당 주가 어떤 달에 들어갈지 결정 / 국가 표준 - 목요일(과반수)
		}
	},
	methods: {
		// doSthWithTodayDate() {
		// 	console.log(this.todayDate.getFullYear())
		// 	console.log(this.theDate)
		// 	console.log(this.theDate.getTime())
		// 	console.log(this.todayDate.getTimezoneOffset())
		// 	console.log(dayjs(this.theDate).format())
		// 	console.log(this.todayDate.getDay())
		// }
	},
	computed: {
		currentYear() {return this.currentRootDate.getFullYear()},
		currentMonth() {return this.currentRootDate.getMonth()},
		currentDate() {return this.currentRootDate.getDate()},
		currentDay() {return dayjs(this.currentRootDate).format('ddd')}
	}
	
}

</script>

<style>
.test {
	display: none;
}

</style>