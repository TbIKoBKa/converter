<template>
  <div class="converter">
			<h1>Currency Converter</h1>
			<label class="label">
				Enter Amount:
				<input type="number" v-model="converter.amount" v-bind:onchange="resultConverter()">
			</label>
			<label class="label">
				<select v-model="converter.from" @click="resultConverter()">
					<option value="EUR">Euro</option>
					<option value="USD">Dollar USA</option>
					<option value="RUR">Rub</option>
					<option value="UAH">Гривна</option>
				</select>
			</label>
			<label class="label">
				<select v-model="converter.to" @click="resultConverter()">
					<option value="EUR">Euro</option>
					<option value="USD">Dollar USA</option>
					<option value="RUR">Rub</option>
					<option value="UAH">Гривна</option>
				</select>
			</label>
			<p>
				<span>{{ `${converter.amount} ${converter.from}` }}</span>
				equals
				<span>{{ `${converter.result} ${converter.to}` }}</span>
			</p>
		</div>
</template>

<script>
import axios from 'axios'

export default {
	name: 'Converter',
	data() {
        return {
			students: [],
			search: '',
			piece: '',
			addForm: {
				mark: '',
				group: '',
				isDonePr: false,
				name: '',
			},
			converter: {
				from: '',
				to: '',
				amount: '',
				result: '',
			},
			ccy: [],
		}
    },
    mounted() {
        axios.get('https://api.privatbank.ua/p24api/pubinfo?json&exchange&coursid=5').then((response) => this.ccy = response.data)
    },
    methods: {
        deleteStudent(studId) {
            this.students = this.students.filter(elem => {
                return elem._id != studId;
            });
        },
        addStudent() {
            const id = this.students.length + 1;
            this.students.push({ id, ...this.addForm });
        },
        resultConverter() {
			const ccy1 = this.ccy.find((ccy) => ccy.ccy === this.converter.from)
			const ccy2 = this.ccy.find((ccy) => ccy.ccy === this.converter.to)

			if((!ccy1 && !ccy2) || (ccy1?.ccy === ccy2?.ccy)) {
				this.converter.result = this.converter.amount
			} else if(!ccy1 && ccy2) {
				this.converter.result = this.converter.amount / ccy2.buy
			} else if (!ccy2) {
				this.converter.result = this.converter.amount * ccy1.buy 
			} else {
				this.converter.result = this.converter.amount * ccy1.buy / ccy2.buy
			}
        },
    },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
