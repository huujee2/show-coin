<template>
	<v-app id="main">
    <v-tabs>
      <v-tab key="tab-1" to="/">가상자산 시세 목록</v-tab>
      <v-tab key="tab-2" to="/favorite">북마크 목록</v-tab>
    </v-tabs>

		<v-row class="table-header">
			<v-col
				class="d-flex"
				cols="15"
				sm="3"
			>
				<v-select
					:items="showModes"
					label="전체보기"
					v-on:input="updateMode($event)"
				></v-select>
			</v-col>
			<v-col
				class="d-flex"
				cols="15"
				sm="3"
			>
				<v-select
					:items="currency"
					label="USD 보기"
					v-on:input="updateCurrency($event)"
				></v-select>
			</v-col>
			<v-col
				class="d-flex"
				cols="15"
				sm="3"
			>
				<v-select
					:items="perPages"
					label="50개 보기"
					v-on:input="updatePerpages($event)"
				></v-select>
			</v-col>
		</v-row>
		
		<v-row class="table-content">
			<v-simple-table>
				<template v-slot:default>
					<thead>
						<tr class="is-selected">
							<th></th>
							<th align="left">자산</th>
							<th></th>
							<th align="right">Price</th>
							<th align="right">1H</th>
							<th align="right">24H</th>
							<th align="right">7D</th>
							<th align="right">24H Volume</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="(value, index) in coins" v-if="setMode == '전체 보기' || value.selected == true">
							<td><input type="checkbox" v-model="value.selected" :value="value.selected" @change="updateFavorite()"></td>
							<td align="left"><router-link :to="{name: 'Detail', params: {name: value.name }}">{{value.name}}</router-link></td>
							<td>{{value.symbol}}</td>
							<td align="right"><label v-if="setCurrency == 'USD'">$</label><label v-else>\</label>{{value.current_price ? numberWithCommas(value.current_price.toFixed(2)) : '-'}}</td>
							<td align="right" v-bind:style="[value.price_change_percentage_1h_in_currency > 0 ? {color:'red'} : {color:'blue'}]">{{value.price_change_percentage_1h_in_currency ? value.price_change_percentage_1h_in_currency.toFixed(2) : '-'}}</td>
							<td align="right" v-bind:style="[value.price_change_percentage_24h_in_currency > 0 ? {color:'red'} : {color:'blue'}]">{{value.price_change_percentage_24h_in_currency ? value.price_change_percentage_24h_in_currency.toFixed(2) : '-'}}</td>
							<td align="right" v-bind:style="[value.price_change_percentage_7d_in_currency > 0 ? {color:'red'} : {color:'blue'}]">{{value.price_change_percentage_7d_in_currency ? value.price_change_percentage_7d_in_currency.toFixed(2) : '-'}}</td>
							<td align="right"><label v-if="setCurrency == 'USD'">$</label><label v-else>\</label>{{numberWithCommas(value.total_volume)}}</td>
						</tr>
					</tbody>
				</template>
			</v-simple-table>
		</v-row>
<v-btn icon color="pink" ><v-icon>mdi-heart</v-icon></v-btn>
		<button
			type="button"
			v-on:click="appendData()"
		>+더보기</button>
		<v-snackbar v-model="snackbar">
			북마크가 설정되었습니다
			<template v-slot:action="{ attrs }">
				<v-btn
				color="pink"
				text
				v-bind="attrs"
				@click="snackbar = false"
				>
				Close
				</v-btn>
			</template>
		</v-snackbar>
	</v-app>
</template>


<script>
import axios from 'axios'

export default {
	name: 'Main',
	data: () => ({
		geturl: '',
		coins: [],
		errors: [],
		showModes: ['전체 보기','북마크 보기'],
		setMode: '전체 보기',
		currency: ['USD 보기','KRW 보기'],
		setCurrency: 'USD',
		perPages: ['50개 보기','30개 보기','10개 보기'],
		setPerpages: '50',
		curPage: 1,
		favorites: [],
		snackbar: false,
	}),
	created () {
		this.coins = JSON.parse(localStorage.getItem('favorites'));
		if(this.coins == null || this.coins.length == 0) {
			this.updateData();
		}
		console.log(this.coins);
	},
	methods : {
		updateData: function() {
			this.geturl = 'https://api.coingecko.com/api/v3/coins/markets?price_change_percentage=1h,24h,7d&vs_currency='+this.setCurrency+'&per_page='+this.setPerpages;
			axios.get(this.geturl)
			.then(response => {
				this.coins = response.data;
				localStorage.setItem('favorites', JSON.stringify(this.coins));
				console.log(response);
			})
			.catch(e => {
				this.errors.push(e)
			});
		},
		numberWithCommas : (x) => {
			if(x)
				return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
			else
				return x;
		},
		updateMode: function (value) {
			console.log(value);
			console.log(this.coins);
			switch(value) {
				case '전체 보기':
					this.setMode = value;
					break;
				case '북마크 보기':
					this.setMode = value;
					break;
			}
		},
		updateCurrency: function (value) {
			console.log(value);
			this.curPage = 1;
			switch(value) {
				case 'USD 보기':
					this.setCurrency = 'USD';
					break;
				case 'KRW 보기':
					this.setCurrency = 'KRW';
					break;
			}
			this.updateData();
		},
		updatePerpages: function (value) {
			console.log(value);
			this.curPage = 1;
			switch(value) {
				case '50개 보기':
					this.setPerpages = '50';
					break;
				case '30개 보기':
					this.setPerpages = '30';
					break;
				case '10개 보기':
					this.setPerpages = '10';
					break;
			}
			this.updateData();
		},
		appendData: function () {
			this.curPage++;
			axios.get(this.geturl+'&page='+this.curPage)
			.then(response => {
				//this.coins.concat(response.data);
				response.data.forEach(item => this.coins.push(item));
				console.log(response.data);
				console.log(this.coins);
			})
			.catch(e => {
				this.errors.push(e);
			});
		},
		updateFavorite: function () {
			localStorage.setItem('favorites', JSON.stringify(this.coins));
			this.snackbar = true;
		}
	}

}
</script>
