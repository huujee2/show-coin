<template>
	<v-app id="detail">
		<div>
            <li>코인 상세 페이지</li>

            <img :src="coin.image" height="50px" width="50px"><h1>{{ name }} ({{coin.symbol}})</h1>
            <h2>거래가: {{numberWithCommas(coin.current_price)}}</h2>
            <h3>시가총액 Rank: #{{coin.market_cap_rank}}</h3>
            <h3>시가총액: {{numberWithCommas(coin.total_volume)}}</h3>
            <h3>24시간 거래대금: {{numberWithCommas(coin.market_cap_change_24h)}}</h3>
        </div>
	</v-app>
</template>


<script>
import axios from 'axios'

export default {
    name: 'Detail',
    props: {
        name: {
            type: String,
            default: ''
        }
    },
	data: () => ({
        coins: [],
        coin: [],
	}),
	created () {
		this.coins = JSON.parse(localStorage.getItem('favorites'));
		console.log(this.coins);
        console.log(this.name);

        var name = this.name;
        var coin = null;
        this.coins.forEach(function(item) {
            if(item.name == name) {
                coin = item;
                return false;
            }
        });
        this.coin = coin;

        console.log(this.coin);
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
