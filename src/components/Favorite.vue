<template>
	<v-app id="favorite">
		<v-tabs>
			<v-tab key="tab-1" to="/">가상자산 시세 목록</v-tab>
			<v-tab key="tab-2" to="/favorite">북마크 목록</v-tab>
		</v-tabs>

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
						<tr v-for="(value, index) in coins" v-if="value.selected == true">
							<td></td>
							<td align="left">{{value.name}}</td>
							<td>{{value.symbol}}</td>
							<td align="right">{{value.current_price ? numberWithCommas(value.current_price.toFixed(2)) : '-'}}</td>
							<td align="right">{{value.price_change_percentage_1h_in_currency ? value.price_change_percentage_1h_in_currency.toFixed(2) : '-'}}</td>
							<td align="right">{{value.price_change_percentage_24h_in_currency ? value.price_change_percentage_24h_in_currency.toFixed(2) : '-'}}</td>
							<td align="right">{{value.price_change_percentage_7d_in_currency ? value.price_change_percentage_7d_in_currency.toFixed(2) : '-'}}</td>
							<td align="right">{{numberWithCommas(value.total_volume)}}</td>
						</tr>
					</tbody>
				</template>
			</v-simple-table>
		</v-row>
	</v-app>
</template>


<script>
import axios from 'axios'

export default {
	name: 'Favorite',
	data: () => ({
		coins: [],
		errors: []
	}),
	created () {
		this.coins = JSON.parse(localStorage.getItem('favorites'));
		console.log(this.coins);
	},
	methods : {
		numberWithCommas : (x) => {
			if(x)
				return x.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
			else
				return x;
		},
	}
}
</script>
