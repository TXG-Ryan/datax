<template>
<div>
  <div class="page-toolbar">
        <div class="layout">
          <div class="pt-wrapper">
            <div class="pt-left">
              <div class="category-selectors"><div class="category-text">Category:</div>
                <div class="selector-button active"><div>All</div></div>
                <div class="selector-button"><div>DeFi</div></div>
                <div class="selector-button"><div>Metaverse</div></div>
                <div class="selector-button"><div>Stablecoins</div></div>
              </div>
              <div class="divider"></div>
              <div class="toolbar-button active"><div>More Filters</div></div>
            </div>
            <div class="pt-right">
              <div class="search-wrapper">
                <div class="search-block">
                    <input type="email" class="search-input" maxlength="256" name="Search-2" data-name="Search 2" placeholder="Search" id="Search-2" required="">
                    <div class="div-block-2">/</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
  <div class="movers">
    <div class="layout">
      <div class="movers-wrapper">
        <div class="table-wrapper">
          <div class="table-header">
            <div class="table-heading first"><div class="table-heading-text">#</div></div>
            <div class="table-heading second"><div class="table-heading-text">Name</div></div>
            <div class="table-heading medium"><div class="table-heading-text">Price</div></div>
            <div class="table-heading small"><div class="table-heading-text">1h</div></div>
            <div class="table-heading small"><div class="table-heading-text">24h / U+03c3</div></div>
            <div class="table-heading small"><div class="table-heading-text">7d</div></div>
            <div class="table-heading xl"><div class="table-heading-text">24h volume</div></div>
            <div class="table-heading xl"><div class="table-heading-text">market cap</div></div>
            <div class="table-heading xxl"><div class="table-heading-text">circulating supply</div></div>
          </div>
          <div class="table-row" v-for="value in coins" :key="value">
            <div class="row-text first">
              <div class="value rank">{{ value.market_cap_rank }}</div>
              </div>
            <div class="row-text id">
              <img :src="value.image" loading="eager" alt="" class="asset-logo">
              <div class="value assets-name">{{value.name}}</div>
              <div class="asset-ticker">{{value.symbol}}</div>
            </div>
            <div class="row-cell medium">
              <div class="value price">{{ currencyFormatter(value.current_price) }}</div>
              <div :class="numberValueStyler(value.price_change_24h)">
                {{ currencyFormatter(value.price_change_24h) }}
              </div>
            </div>
            <div class="row-cell small">
                <div :class="percentValueStyler(value.price_change_percentage_1h_in_currency)">
                  {{ parseFloat( value.price_change_percentage_1h_in_currency?.toLocaleString() ).toFixed(2) }}%
                </div>
            </div>
            <div class="row-cell small">
                <div :class="percentValueStyler(value.price_change_percentage_24h_in_currency)">
                  {{ parseFloat( value.price_change_percentage_24h_in_currency?.toLocaleString() ).toFixed(2) }}%
                </div>
                <div :class="percentValueStyler(value.datax.h24_vol)">
                  {{ value.datax.h24_vol }}
                </div>
            </div>
            <div class="row-cell small">
                <div :class="percentValueStyler(value.price_change_percentage_7d_in_currency)">
                  {{ parseFloat( value.price_change_percentage_7d_in_currency?.toLocaleString() ).toFixed(2) }}%
                </div>
                <div :class="percentValueStyler(value.datax.d7_vol)">
                  {{ value.datax.d7_vol }}
                </div>
            </div>
            <div class="row-cell xl">
              <div class="value">${{ numAbbr(value.total_volume) }}</div>
              <div class="value-muted">{{ numAbbr(Math.abs(value.total_volume / value.current_price)) }} {{ value.symbol.toUpperCase() }}</div>
            </div>
            <div class="row-cell xl">
              <div class="value">${{ numAbbr(value.market_cap) }}</div>
              <div :class="numberValueStyler(value.market_cap_change_percentage_24h)">{{ parseFloat( value.market_cap_change_percentage_24h ).toFixed(2) +'%'}}</div>
            </div>
            <div class="row-cell xxl">
              <div class="value">{{ numAbbr(value.circulating_supply) }} {{ value.symbol.toUpperCase() }}</div>
              <div class="supply-bar-wrapper">
                <div class="supply" :style="{width: Math.floor((value.circulating_supply / value.total_supply) * 100)+'%'}"></div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import axios from 'axios'

export default {
    data: () => ({
        coins: [],
        errors: [],
        timer: [],

    }),

    created() {

        this.getPrice();
        this.timer = setInterval(this.getPrice, 10000);

    },

    methods: {
        async getPrice() {
            axios
            .get('http://coindata.ddns.net/scripts/coinstats.php')
            .then(response => { this.coins = response.data
            console.log(response)})
            .catch(e => { this.errors.push(e)})
        },

      percentValueStyler(value) {
        return {
          'positivePct': value > 0 && value <= 25,
          'positivePctLarge': value > 25,
          'negativePct': value < 0 && value >= -25,
          'negativePctLarge': value < 25,
          'neutralPct': value = 0,

        };
      },
      numberValueStyler(value) {
        return {
          'positiveNum': value > 0,
          'negativeNum': value < 0,
          'neutralNum': value = 0,

        };
      },

      numAbbr(value) {
          if (value >= 1000000000) {
              return (value / 1000000000).toFixed(2).replace(/\.0$/, '') + 'B';
          }
          if (value >= 1000000) {
              return (value / 1000000).toFixed(2).replace(/\.0$/, '') + 'M';
          }
          if (value >= 1000) {
              return (value / 1000).toFixed(2).replace(/\.0$/, '') + 'K';
          }
          return value;
      },

      currencyFormatter(value) {
          return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
      },
    },
}
</script>

<style scoped>
.percentage-cell {
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  padding: 6px 8px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  background-color: #e9ebf1;
  line-height: 21px;
}

.positivePct {
  background-color: rgba(4, 165, 141, 0);
  color: #00ad6b;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  padding: 6px 8px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  line-height: 21px;
  font-weight: 500;
  padding-right: 16px;
  background-image: url('../assets/images/arrow-up-right.svg');
  background-position: 100% 50%;
  background-size: 13px;
  background-repeat: no-repeat;
}

.positivePct {
  background-color: rgba(4, 165, 141, 0);
  color: #00ad6b;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  padding: 6px 8px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  line-height: 21px;
  font-weight: 500;
  padding-right: 12px;
  background-image: url('../assets/images/arrow-up-right.svg');
  background-position: 100% 50%;
  background-size: 13px;
  background-repeat: no-repeat;
}

.positivePctLarge {
  background-color: rgba(4, 165, 141, 0);
  color: #00ad6b;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  line-height: 21px;
  font-weight: 500;
  padding-right: 16px;
  background-image: url('../assets/images/on-fire.gif');
  background-position: 100% 50%;
  background-size:18px;
  background-repeat: no-repeat;
}

.negativePct {
  background-color: rgba(217, 71, 90, 0);
  color: #d9475a;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  padding: 6px 8px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  line-height: 21px;
  font-weight: 500;
  padding-right: 12px;
  background-image: url('../assets/images/arrow-down-right.svg');
  background-position: 100% 50%;
  background-size: 13px;
  background-repeat: no-repeat;
}

.negativeNum {
  color: #d9475a;
  font-size: 12px;
  line-height: 22.5px;
  padding-right: 16px;
  background-image: url('../assets/images/arrow-down-right.svg');
  background-position: 100% 50%;
  background-size: 10px;
  background-repeat: no-repeat;
}

.positiveNum {
  color: #00ad6b;
  font-size: 12px;
  line-height: 22.5px;
  padding-right: 16px;
  background-image: url('../assets/images/arrow-up-right.svg');
  background-position: 100% 50%;
  background-size: 10px;
  background-repeat: no-repeat;
}

.neutralNum {
  color: #7b849b;
  font-size: 12px;
  line-height: 22.5px;
}

.neutralPct {
  background-color: rgba(255, 255, 255, 0);
  color: #7b849b;
  display: -webkit-box;
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  width: 80px;
  padding: 6px 8px;
  -webkit-box-pack: center;
  -webkit-justify-content: center;
  -ms-flex-pack: center;
  justify-content: center;
  -webkit-box-align: center;
  -webkit-align-items: center;
  -ms-flex-align: center;
  align-items: center;
  border-radius: 8px;
  line-height: 21px;
}
</style>
